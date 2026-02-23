---
title: "DatabricksでSupervisor Agentを構築してみた"
emoji: "🤖"
type: "tech"
topics: ["Databricks", "LangGraph", "AIAgent", "MLflow", "Python"]
published: false
publication_name: "ivry"
---


こんにちは、IVRy でデータエンジニアとして働いている松田 健司([@ken_3ba](https://x.com/ken_3ba))です。趣味はビリヤードで、プロの試合にも出ているぐらい割とガチでやっています。

今年に入って、ビリヤードの世界大会で日本人の女性と男性がそれぞれ優勝しました！ビリヤードはマイナースポーツなのでご存知ない方も多いかもしれませんが、これは本当にすごいことで、実際にお会いしているプロの方だったので感動しました！

そして、優勝するとビリヤード台の上でパフォーマンスをするという慣例があるのですが、その優勝した日本人プロの方は靴を脱いで台に上がり、日本人らしさを感じました笑

![優勝のポスター（引用元: https://www.billiards-days.com/20260210-1/）](/images/databricks-ai-agent-supervisor.md/billiards.jpg)

さて、本日のビリヤードの話はこのへんで切り上げて本題に入ります！

# はじめに

今回は、社内の業務効率化のためにAI Agentの仕組みをDatabricks上に構築した話をします。具体的には、LangGraphを使ったSupervisor Agentの設計から、Databricks上でのAgent作成、MLflowを利用した評価までを紹介します。

私は半年前までAI Agentを使ったことすらないレベルでしたが、Databricksを使うことでかなり簡単にいいものができました。ぜひまだAgentを作成したことがない同じ境遇の方の参考になれば幸いです。

# なぜAgentを作ったのか

IVRyはAIによる電話自動応答サービスを提供している会社であり、社内でも営業活動の中でアイブリーを利用しています。そこから得られるデータやビジネスコミュニケーションデータを活用すれば、セールスチームの業務を支援する社内向けサービスが作れるのではないかと考え、実験を進めています。

例えば、アポイントメント電話の終話直後に次回のミーティング資料が自動生成されている、といった体験を目指しています。現在はダミーデータを使いながら検証しています。

![AI Agentでセールス活動の効率化](/images/databricks-ai-agent-supervisor.md/ysdyt.png)
*引用: [Databricks After Party 2025 LTスライド](https://ysdyt.dev/posts/2025/12/databricks-after-party-2025-LT)*

弊社ではあらゆるデータがDatabricksに集まっているため、その環境を活かしてDatabricksと親和性が高いLangGraphベースのAI Agentを構築しました。実験的に作成したのが、Agentが複数の異なるデータソースにアクセスしながら、ユーザーがボタン一つで資料のドラフトを作成できるサービスです。

1つのAgentで全てを処理するのではなく、役割ごとにAgentを分割し、それを統括するSupervisor Agentが全体を制御するアーキテクチャを採用しました。

# Agentの設計

## Supervisor Agentにした理由

Supervisor Agentパターンとは、1つの親Agentがユーザーのリクエストを受け取り、内容を解析した上で適切なSub Agentに処理を委譲するアーキテクチャです。各Sub Agentは専門的な役割を持ち、必要なツールやデータにアクセスして結果を返します。Supervisor Agentはそれらの結果を統合し、最終的な回答をユーザーに返します。

このパターンを採用した理由は以下です。

1. **関心の分離**: 各Sub Agentが専門領域に特化することで、プロンプトの複雑さを抑えられる
2. **拡張性**: 新しい機能を追加する際、新しいSub Agentを追加するだけで対応可能
3. **デバッグのしやすさ**: どのAgentでどのような処理が行われたかが明確になる

## アーキテクチャの全体像

実際に構築したマルチAgentは以下のような構成です。

![Supervisor Agentの構成](/images/databricks-ai-agent-supervisor.md/supervisor_agent.png)

- **Supervisor Agent**: ユーザーからのリクエストを受け取り、適切なSub Agentにルーティングし、回答を統合してユーザーに返す
- **情報取得Agent**: データ基盤上の企業の基本情報を検索・取得
- **議事録取得Agent**: ミーティングの議事録データを取得・要約
- **提案資料作成Agent**: 収集した情報をもとに提案資料のドラフトを生成

データはUnity Catalogに格納されており、UDFsを通じてアクセスします。

# やってみた

## UDFsの作成

Agentが自由にデータにアクセスすると予期せぬ挙動が起きるため、Databricksの[UDFs](https://docs.databricks.com/gcp/en/udf/unity-catalog)を作成して制限しました。これによりデータ取得の揺らぎを最小限にしています。

以下のようにSQLを実行すればUnity Catalog上に関数を作ることができます。ただ、まだTerraformで管理できないので、今後のアップデートが待ち遠しいですね。

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

各Sub Agentは、Pythonファイルとして定義します。以下は企業情報取得Agentの例です。LangGraphの`create_react_agent`を使い、UDFsをツールとして渡すだけでAgentが作れます。ポイントは以下の4ステップです。

1. **`mlflow.models.ModelConfig()`** — MLflow登録時に渡される設定値を取得する
2. **`UCFunctionToolkit`** — Unity Catalogに登録したUDFsをAgentのツールとして利用可能にする。Agentがデータにアクセスする手段をUDFs経由に制限できる
3. **`create_react_agent`** — LLMとツールを組み合わせてReActパターンのAgentを作成する。LLMが「考えて→ツールを実行→結果を見て判断」を繰り返す仕組みを数行で構築できる
4. **`mlflow.models.set_model`** — このAgentをMLflowのモデルとして登録可能にする。これにより`log_model`でMLflowに記録できるようになる

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

作成したAgent定義ファイルを、**MLflow**を使ってUnity Catalogに登録します。これにより、バージョン管理やデプロイが容易になります。

まずはトレーシングの有効化です。トレーシングを有効にすると実行時のプロンプト情報などが記録され、後の評価に役立ちます。

```python
import mlflow
from mlflow.models import infer_signature

CATALOG = "dummy_catalog"
SCHEMA = "dummy_schema"
LLM_ENDPOINT = "databricks-meta-llama-3-3-70b-instruct"
WAREHOUSE_ID = "xxxxxxxxxxxxxxxx"

mlflow.langchain.autolog(log_traces=True)
```

次に、Agent定義ファイルをMLflowに記録し、Unity Catalogに登録する関数を定義します。ポイントは以下の3ステップです。

1. **`mlflow.langchain.log_model`** — AgentのPythonファイルをMLflowのExperimentにアーティファクトとして記録する。この時点ではまだ外部から参照できない「下書き」のような状態
2. **`mlflow.register_model`** — 記録したモデルをUnity Catalogに正式に登録する。これにより `models:/catalog.schema.model_name` というURIで他のコードから参照可能になる
3. **エイリアスの設定** — 登録したモデルに `latest` などの別名を付ける。バージョン番号の代わりに `models:/catalog.schema.model_name@latest` のように名前で参照できるため、モデル更新時に呼び出し側のコード変更が不要になる

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

次にSub Agentを呼び出すSupervisor Agentを登録します。定義ファイルは以下のようになります。Supervisor Agentのポイントは、Sub Agentを`@tool`で定義してツールとして呼び出す点です。

1. **`load_agent_model`** — Unity Catalogに登録済みのSub Agentをロードする関数を定義する。エイリアスで参照するため、Sub Agentが更新されてもSupervisor側のコード変更は不要
2. **`@tool`デコレータ** — 各Sub Agentの呼び出しをLangChainのツールとして定義する。これによりSupervisorのLLMがどのSub Agentを呼ぶか自律的に判断できるようになる
3. **`create_react_agent`** — Supervisor自身もReActパターンのAgentとして作成する。Sub Agentと同じ仕組みで構築できるため、アーキテクチャが統一される

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

Sub Agentと同様に`register_agent`で登録します。`extra_config`でSub Agentのバージョンやカタログ情報を渡すことで、Supervisor Agentの定義ファイル内から`ModelConfig`経由で参照できるようにしています。

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

実際に実行すると、Supervisorが質問内容を解析し、以下のような流れで処理されます。

1. **Supervisor**が質問を解析し、必要なSub Agentを判断
2. **企業情報取得Agent**が「〇〇株式会社」の企業情報を検索
3. **議事録取得Agent**が〇〇株式会社に関連するミーティングの議事録を取得
4. **資料作成Agent**が収集した情報をもとに提案資料のドラフトを生成
5. **Supervisor**が結果を統合してユーザーに回答

# MLflowを使ってみて便利だったこと

今回利用したMLflowについて、特に便利だった機能を紹介します。

## MLflow Tracing

`mlflow.langchain.autolog()` を有効にすると、Agentの処理フローが自動的にトレーシングされます。SupervisorがどのSub Agentを呼び出し、どのような結果が返ってきたかが可視化されるため、デバッグやモデルの改善に役立ちます。

![MLflow Tracing](/images/databricks-ai-agent-supervisor.md/mlflow_trace.avif)
*引用: [DatabricksにおけるMLflow Tracing](https://qiita.com/taka_yayoi/items/35c96ecd401c199e617b)*

## 評価

MLflowの評価機能を使うことで、Agentの回答品質を定量的に評価できます。事前に用意した質問と期待する回答のペアに対して、実際のAgentの回答がどの程度一致するかを測定できます。

![MLflow Evaluate](/images/databricks-ai-agent-supervisor.md/evaluate.avif)
*引用: [MLflow 2.4のmlflow.evaluateとアーティファクトビューでLLMの評価が捗る件](https://qiita.com/taka_yayoi/items/f06adbf5510703b0510b)*

## モデルのバージョン管理とデプロイ

MLflowでモデルをバージョン管理できるため、プロンプトやモデルの変更ごとに結果を比較し、より良いバージョンに切り替えることが可能です。また、Databricks Asset Bundles（DAB）を使えば、Model Servingとして簡単にAPIとしてデプロイできます。外部からAPI経由で利用することも可能です。

![モデルのバージョン管理](/images/databricks-ai-agent-supervisor.md/mode_version.avif)
*引用: [Workspace Model Registry の例](https://docs.databricks.com/aws/ja/mlflow/workspace-model-registry-example)*

# Agent Bricksへの期待

現在、Databricksでは**Agent Bricks**（Mosaic AI Agent Framework）がすでに海外リージョンで公開されており、より簡単にAgentを作成できるようになり、とても待ち遠しいです。
https://docs.databricks.com/aws/ja/generative-ai/agent-bricks/

DatabricksのAgent関連の機能はアップデートが非常に活発で、新機能のリリースが続いており、毎回りリースを楽しみにしています！

# まとめ

本記事では、Databricks上でSupervisor Agentを構築した事例を紹介しました。

Databricksのエコシステムを活用することで、開発からデプロイまでを一気通貫で行える環境が整っています。今までAI Agentを作ったことがなく、社内で実験的に試してみたい方にはお勧めです。さらにDatabricks Appsと組み合わせればAgentを使った社内アプリも簡単に構築できます。こういったトータルソリューションで展開できるところもDatabricksの魅力の一つです。

---

IVRyでは「イベントや最新ニュース、募集ポジションの情報を受け取りたい」「会社について詳しく話を聞いてみたい」といった方に向けて、キャリア登録やカジュアル面談の機会をご用意しています。ご興味をお持ちいただけた方は、ぜひ以下のページよりご登録・お申し込みください。

https://herp.careers/v1/ivry/wmZiOSAmZ4SQ
https://www.notion.so/209eea80adae800483a9d6b239281f1b?pvs=21
