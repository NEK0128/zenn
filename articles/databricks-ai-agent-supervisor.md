---
title: "DatabricksでSupervisor Agentを構築してみた"
emoji: "🤖"
type: "tech"
topics: ["Databricks", "LangGraph", "AIAgent", "MLflow", "Python"]
published: false
publication_name: "ivry"
---


こんにちは、IVRy でデータエンジニアとして働いている松田 健司([@ken_3ba](https://x.com/ken_3ba))です。趣味はビリヤードで、プロの試合にも出ているぐらい割とガチでやっています。

今年に入って、ビリヤードの世界大会で日本人の女性と男性がそれぞれ優勝しました！ビリヤードはマイナースポーツなのでご存知ない方も多いかもしれませんが、これは本当にすごいことでとても感動しました！

そして、優勝するとビリヤード台の上でパフォーマンスをするという慣例があるのですが、その優勝した日本人プロの方は靴を脱いで台に上がり、日本人らしさを感じました笑

![優勝のポスター（引用元: https://www.billiards-days.com/20260210-1/）](/images/databricks-ai-agent-supervisor.md/billiards.jpg)

さて、本日のビリヤードの話はこのへんで切り上げて本題に入ります！

# はじめに

今回は、業務効率化のためにDatabricks上でLangGraphベースのSupervisor Agentを構築した話をします。設計からMLflowでの登録・評価までを紹介します。

半年前までAI Agentを触ったことすらなかった自分でも、Databricksを使えばかなり簡単に構築できました。同じ境遇の方の参考になれば幸いです。

# なぜAgentを作ったのか

IVRyはAIによる電話自動応答サービスを提供しており、社内でも営業活動にアイブリーを利用しています。そこから得られるデータを活用し、セールスチームの業務を支援する社内サービスを実験的に開発しています。

例えば、アポイント電話の終話直後にミーティング資料が自動生成されている、といった体験を目指しています。

![AI Agentでセールス活動の効率化](/images/databricks-ai-agent-supervisor.md/ysdyt.png)
*引用: [Databricks After Party 2025 LTスライド](https://ysdyt.dev/posts/2025/12/databricks-after-party-2025-LT)*

弊社ではあらゆるデータがDatabricksに集まっているため、LangGraphベースのAI Agentを構築しました。複数のデータソースから情報を集め、ボタン一つで資料のドラフトを作成できるサービスです。

役割ごとにAgentを分割し、Supervisor Agentが全体を制御するアーキテクチャを採用しました。

# Agentの設計

## Supervisor Agentにした理由

Supervisor Agentパターンとは、親Agentがリクエストを解析し、適切なSub Agentに処理を委譲するアーキテクチャです。各Sub Agentが結果を返し、Supervisorがそれを統合して回答します。

採用した理由は以下です。

1. **関心の分離**: 各Sub Agentが専門領域に特化し、プロンプトの複雑さを抑えられる
2. **拡張性**: 新しいSub Agentを追加するだけで機能追加できる
3. **デバッグのしやすさ**: どのAgentで何が行われたか明確になる

## アーキテクチャの全体像

実際に構築したMulti-Agentは以下のような構成です。

![Supervisor Agentの構成](/images/databricks-ai-agent-supervisor.md/supervisor_agent.png)

- **Supervisor Agent**: リクエストを受け取り、Sub Agentにルーティングし、結果を統合して返す
- **企業情報取得Agent**: 企業の基本情報を検索・取得
- **議事録取得Agent**: ミーティングの議事録を取得・要約
- **提案資料作成Agent**: 収集した情報から提案資料のドラフトを生成

データはUnity Catalogに格納し、UDFs経由でアクセスします。

# やってみた

## UDFsの作成

Agentが自由にSQLを発行するとスキーマの誤認識や不要なデータ取得が起きるため、Databricksの[UDFs](https://docs.databricks.com/gcp/en/udf/unity-catalog)でデータアクセスを制限しています。

以下のようにSQLでUnity Catalog上に関数を作れます。まだTerraformで管理できないので、今後のアップデートが待ち遠しいですね。

```sql
CREATE OR REPLACE FUNCTION dummy_catalog.dummy_schema.search_company(
    company_name STRING COMMENT '検索する企業名（部分一致）'
)
RETURNS TABLE(
    account_id STRING COMMENT 'アカウントID',
    name STRING COMMENT '企業名',
    industry STRING COMMENT '業種',
    location STRING COMMENT '所在地'
)
COMMENT '企業名で企業情報を検索します。'
RETURN
    SELECT
        account_id,
        name,
        industry,
        location
    FROM dummy_catalog.dummy_schema.companies
    WHERE name LIKE CONCAT('%', company_name, '%')
    ORDER BY name
    LIMIT 20
```

## Agent定義の作成

各Sub AgentはPythonファイルとして定義します。以下は企業情報取得Agentの例です。`create_react_agent`にUDFsをツールとして渡すだけでAgentが作れます。

1. **`ModelConfig()`** — MLflow登録時の設定値を取得
2. **`UCFunctionToolkit`** — UDFsをAgentのツールとして利用可能にする。データアクセスをUDFs経由に制限できる
3. **`create_react_agent`** — LLMとツールでReActパターンのAgentを作成
4. **`set_model`** — MLflowのモデルとして登録可能にする

```python
import mlflow
from langchain_community.chat_models import ChatDatabricks
from langchain_community.tools.databricks import UCFunctionToolkit
from langgraph.prebuilt import create_react_agent

# Step 1: MLflow登録時に渡される設定値を取得
model_config = mlflow.models.ModelConfig()
LLM_ENDPOINT = model_config.get("llm_endpoint")
WAREHOUSE_ID = model_config.get("warehouse_id")

UC_FUNCTIONS = [
    "dummy_catalog.dummy_schema.search_company",
]

SYSTEM_PROMPT = """あなたは企業情報取得エージェントです。
入力された企業名から企業情報を検索し、以下のJSON形式で返してください。
{"account_id": "アカウントID", "name": "企業名", "industry": "業種", "location": "所在地"}
見つからない場合はnullを返し、理由を記載してください。
"""

# Step 2: UDFsをAgentのツールとして利用可能にする

toolkit = UCFunctionToolkit(warehouse_id=WAREHOUSE_ID, function_names=UC_FUNCTIONS)
tools = toolkit.get_tools()

# Step 3: LLMとツールを組み合わせてReActパターンのAgentを作成

llm = ChatDatabricks(endpoint=LLM_ENDPOINT, temperature=0.1)
agent = create_react_agent(llm, tools, prompt=SYSTEM_PROMPT)

# Step 4: このAgentをMLflowのモデルとして登録可能にする

mlflow.models.set_model(agent)
```

## MLflowでのSub Agent登録

作成したAgent定義ファイルをMLflowでUnity Catalogに登録します。まずトレーシングを有効化します。

```python
import mlflow
from mlflow.models import infer_signature

CATALOG = "dummy_catalog"
SCHEMA = "dummy_schema"
LLM_ENDPOINT = "databricks-meta-llama-3-3-70b-instruct"
WAREHOUSE_ID = "xxxxxxxxxxxxxxxx"

mlflow.langchain.autolog(log_traces=True)
```

次に、Agent定義ファイルをMLflowに記録し、Unity Catalogに登録する関数を定義します。

1. **`log_model`** — AgentのPythonファイルをMLflowに記録する。まだ外部から参照できない「下書き」状態
2. **`register_model`** — Unity Catalogに正式登録し、`models:/catalog.schema.model_name` のURIで参照可能にする
3. **エイリアス設定** — `@latest` などの別名を付け、モデル更新時に呼び出し側のコード変更を不要にする

```python
def register_agent(
    catalog: str,
    schema: str,
    model_name: str,
    definition_file: str,
    llm_endpoint: str,
    warehouse_id: str,
    input_example: dict,
    extra_config: dict | None = None,
) -> dict:
    """Agent定義ファイルをUnity Catalogに登録する。"""

    model_config = {
        "llm_endpoint": llm_endpoint,
        "warehouse_id": warehouse_id,
    }
    if extra_config:
        model_config.update(extra_config)

    # Step 1: AgentをMLflowに記録
    with mlflow.start_run(run_name=model_name):
        model_info = mlflow.langchain.log_model(
            lc_model=os.path.join(os.getcwd(), definition_file),
            name="agent",
            input_example=input_example,
            model_config=model_config,
        )

    # Step 2: Unity Catalogに登録
    registered_model_name = f"{catalog}.{schema}.{model_name}"
    mlflow.set_registry_uri("databricks-uc")
    registered_model = mlflow.register_model(
        model_uri=model_info.model_uri,
        name=registered_model_name,
    )

    # Step 3: エイリアスを設定
    client = mlflow.tracking.MlflowClient()
    client.set_registered_model_alias(
        name=registered_model_name,
        alias="latest",
        version=registered_model.version,
    )

    return {
        "model_uri": model_info.model_uri,
        "registered_model_name": registered_model_name,
        "version": registered_model.version,
    }
```

あとはこの関数を呼び出すだけで、各Sub Agentを登録できます。

```python
result = register_agent(
    catalog=CATALOG,
    schema=SCHEMA,
    model_name="company_search_agent",
    definition_file="company_search_agent_definition.py",
    llm_endpoint=LLM_ENDPOINT,
    warehouse_id=WAREHOUSE_ID,
    input_example={
        "messages": [{"role": "user", "content": "〇〇株式会社を検索してください"}]
    },
)
print(f"Agent 登録完了! Version: {result['version']}")
```

## MLflowでのSupervisor Agent登録

次にSupervisor Agentを登録します。ポイントはSub Agentを`@tool`でツール化し、Supervisorから呼び出す点です。

1. **`load_agent_model`** — Unity Catalog登録済みのSub Agentをエイリアスでロード。Sub Agent更新時もSupervisor側の変更不要
2. **`@tool`** — 各Sub AgentをLangChainのツールとして定義。SupervisorのLLMがどのSub Agentを呼ぶか自律的に判断する
3. **`create_react_agent`** — Supervisor自身もReActパターンで作成。Sub Agentと同じ仕組みでアーキテクチャが統一される

```python
import mlflow
from langchain_community.chat_models import ChatDatabricks
from langchain_core.tools import tool
from langgraph.prebuilt import create_react_agent

model_config = mlflow.models.ModelConfig()
LLM_ENDPOINT = model_config.get("llm_endpoint")
CATALOG = model_config.get("catalog")
SCHEMA = model_config.get("schema")

mlflow.set_registry_uri("databricks-uc")


# Step 1: Unity Catalogから登録済みSub Agentをロード
def load_agent_model(agent_name: str):
    model_uri = f"models:/{CATALOG}.{SCHEMA}.{agent_name}@latest"
    return mlflow.langchain.load_model(model_uri)


# Step 2: 各Sub Agentをツールとして定義
@tool
def call_company_search(company_name: str) -> str:
    """企業名から企業情報を検索します。"""
    agent = load_agent_model("company_search_agent")
    result = agent.invoke(
        {"messages": [{"role": "user", "content": f"{company_name}を検索してください"}]}
    )
    return result["messages"][-1].content


@tool
def call_meeting_notes(company_name: str) -> str:
    """企業に関連するミーティングの議事録を取得します。"""
    agent = load_agent_model("meeting_notes_agent")
    result = agent.invoke(
        {"messages": [{"role": "user", "content": f"{company_name}の議事録を取得してください"}]}
    )
    return result["messages"][-1].content


@tool
def call_document_generator(context: str) -> str:
    """収集した情報をもとに提案資料のドラフトを生成します。"""
    agent = load_agent_model("document_generator_agent")
    result = agent.invoke(
        {"messages": [{"role": "user", "content": f"以下の情報から資料を作成してください:\n{context}"}]}
    )
    return result["messages"][-1].content


SYSTEM_PROMPT = """あなたはSupervisor（司令塔）エージェントです。
ユーザーの質問を解釈し、適切なSub Agentを呼び出して回答を統合します。

[利用可能なツール]
1. call_company_search: 企業名から企業情報を検索
2. call_meeting_notes: 企業に関連する議事録を取得
3. call_document_generator: 収集した情報から提案資料を生成

[ワークフロー]
1. call_company_search で企業情報を取得
2. call_meeting_notes で関連する議事録を取得
3. call_document_generator で資料のドラフトを生成
"""

# Step 3: Supervisor AgentをReActパターンで作成
tools = [call_company_search, call_meeting_notes, call_document_generator]
llm = ChatDatabricks(endpoint=LLM_ENDPOINT, temperature=0.1)
agent = create_react_agent(llm, tools, prompt=SYSTEM_PROMPT)
mlflow.models.set_model(agent)
```

Sub Agentと同様に`register_agent`で登録します。`extra_config`でSub Agentのバージョン情報を渡し、定義ファイル内から`ModelConfig`経由で参照します。

```python
result_supervisor = register_agent(
    catalog=CATALOG,
    schema=SCHEMA,
    model_name="proposal_supervisor_agent",
    definition_file="proposal_supervisor_agent_definition.py",
    llm_endpoint=LLM_ENDPOINT,
    warehouse_id=WAREHOUSE_ID,
    input_example={
        "messages": [{"role": "user", "content": "〇〇株式会社の提案資料を作成してください"}]
    },
    extra_config={
        "catalog": CATALOG,
        "schema": SCHEMA,
        "company_search_version": result_company_search["version"],
        "meeting_notes_version": result_meeting_notes["version"],
        "document_generator_version": result_document_generator["version"],
    },
)
print(f"Supervisor Agent 登録完了! Version: {result_supervisor['version']}")
```

## Agentを使用する

登録したAgentは`mlflow.langchain.load_model`でロードするだけで実行できます。

```python
import mlflow

mlflow.set_registry_uri("databricks-uc")

# Unity CatalogからAgentをロード
model_uri = "models:/dummy_catalog.dummy_schema.proposal_supervisor_agent@latest"
agent = mlflow.langchain.load_model(model_uri)

# 実行
result = agent.invoke({
    "messages": [{"role": "user", "content": "〇〇株式会社の提案資料を作成してください"}]
})
print(result["messages"][-1].content)
```

実行すると、以下の流れで処理されます。

1. **Supervisor**が質問を解析し、必要なSub Agentを判断
2. **企業情報取得Agent**が企業情報を検索
3. **議事録取得Agent**が関連する議事録を取得
4. **資料作成Agent**が提案資料のドラフトを生成
5. **Supervisor**が結果を統合して回答

# MLflowを使ってみて便利だったこと

特に便利だった機能を紹介します。

## MLflow Tracing

`mlflow.langchain.autolog()` を有効にすると、Agentの処理フローが自動トレーシングされます。どのSub Agentがどんな結果を返したか可視化され、デバッグに役立ちます。

![MLflow Tracing](/images/databricks-ai-agent-supervisor.md/mlflow_trace.png)
*引用: [DatabricksにおけるMLflow Tracing](https://qiita.com/taka_yayoi/items/35c96ecd401c199e617b)*

## 評価

MLflowの評価機能で、モデルごとの出力を並べて比較できます。人間の目で実際の回答を見比べられるので、プロンプトやモデルの改善サイクルを回しやすくなります。

![MLflow Evaluate](/images/databricks-ai-agent-supervisor.md/evaluate.png)
*引用: [MLflow 2.4のmlflow.evaluateとアーティファクトビューでLLMの評価が捗る件](https://qiita.com/taka_yayoi/items/f06adbf5510703b0510b)*

## モデルのバージョン管理とデプロイ

MLflowでモデルをバージョン管理し、変更ごとに結果を比較できます。Databricks Asset Bundles（DAB）を使えばModel Servingとして簡単にAPIデプロイも可能です。

![モデルのバージョン管理](/images/databricks-ai-agent-supervisor.md/mode_version.png)
*引用: [Workspace Model Registry の例](https://docs.databricks.com/aws/ja/mlflow/workspace-model-registry-example)*

# Agent Bricksへの期待

Databricksでは**Agent Bricks**（Mosaic AI Agent Framework）がすでに海外リージョンで公開されています。日本リージョンへの展開が待ち遠しいです。
https://docs.databricks.com/aws/ja/generative-ai/agent-bricks/

# まとめ

Databricks上でSupervisor型Multi-Agentを構築した事例を紹介しました。

Databricksを使えば、Agent定義からMLflowでの登録・評価・デプロイまで一気通貫で行えます。Databricks Appsと組み合わせれば社内アプリも簡単に構築でき、こうしたトータルソリューションがDatabricksの魅力です。

---

IVRyではキャリア登録やカジュアル面談の機会をご用意しています。ご興味のある方はぜひ以下よりお申し込みください。

https://herp.careers/v1/ivry/wmZiOSAmZ4SQ
https://www.notion.so/209eea80adae800483a9d6b239281f1b?pvs=21
