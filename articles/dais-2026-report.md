---
title: "Data + AI Summit 2026 現地参加レポート：データ基盤は「AIがビジネスで意思決定する基盤」へ"
emoji: "🎸"
type: "idea"
topics: ["Databricks", "DataAISummit", "AI", "データエンジニアリング", "IVRy"]
published: true
publication_name: "ivry"
---

こんにちは、IVRyでデータエンジニアとして働いている松田健司（[@ken_3ba](https://x.com/ken_3ba)）です。趣味はビリヤードで、プロの試合にも出ているぐらい割とガチでやっています。

今回はData + AI Summit（以下Summit）に参加してきたので、現地の様子と注目した発表をまとめます。

その前に、いつものビリヤードの話を。Data + AI Summitにはいくつかクエストがあって、達成するとビリヤードボールっぽいものがもらえます。
ビリヤードプレイヤーとしての使命感で集めてきました。

https://x.com/Data_AI_Summit/status/1933245536389198198

https://x.com/ken_3ba/status/2067036954949239180

ちなみに、会場の近くに本物のビリヤード場もあったみたいですが、行けなかったのが心残りです。それでは本題に入ります。

## TL;DR

- AI時代に差がつくのは、派手なAIアプリではなく、AIが正しく働くための「データと業務の土台」をどれだけ握れるか。データ基盤は「人がデータを貯めて見る」ものから、「AIがデータを使って意思決定する」ものへと変わってきている。
- Databricksの通底メッセージは「AGIはもう来ている。足りないのは知能ではなく、その周りのContext / Cost / Control / Choice（4つのC）だ」。Genieファミリーを軸に幅広い領域をUnity Catalog配下に束ね、「Lakehouseはエージェント時代のOS」という像を打ち出していた。
- 現地に行く価値は大きい。発表はオンラインでも追えるが、現場の熱狂の中で聞くと記憶への残り方もモチベーションも段違いで、ぜひみんな参加して欲しいイベントだった。

:::message
「発表された新機能」は数が多いので、気になる機能だけ選んで読んだり、辞書的に逆引きしたりして使ってもらえれば大丈夫です。記事の最後に「[実際に現地へ行ってみた感想](#実際に現地へ行ってみた感想)」もあるので、そこだけでもぜひ読んでみてください。
:::

## IVRyとDatabricks

IVRyは2025年7月、データ・AI活用を加速させるために、Databricksを中心にしてデータ基盤を構築しました。Databricks導入前は、データが増えるにつれて転送コストが膨らみ、データと権限があちこちに散らばるという課題がありました。そのためこれらを解決し、AIをより活用しやすい基盤としてDatabricksをえらんだ、という背景があります。

そして、メール・チャットなどのコミュニケーションデータを一元的に統合・解析するデータプラットフォーム「IVRy Data Hub」というプロダクトも、このDatabricks基盤の上で動いています。

https://ivry.jp/function/datahub/

導入の背景は、以下の登壇資料が詳しいので、こちらをどうぞ。

https://speakerdeck.com/keisukeosone/gendatoivry-ji-cheng-chang-sutatoatupugadatabrickswocai-yong-sitali-you-data-plus-ai-world-tour-2025

## Databricks Data + AI Summitとは

Data + AI Summitは、Databricksが年に一度開催する世界最大級のデータ・AIのカンファレンスです。毎年、サンフランシスコのMoscone Centerで開催されています。今年は6月15日から18日まででした。

https://www.databricks.com/dataaisummit

前回はIVRyの別のメンバーが参加していたのですが、その1人が「今年は規模が明らかに大きくなっていた」と言っていました。現地参加は174カ国から31,309名。日本からの参加者もかなり増えていた印象です。そして何より、今回も発表される機能がとても多かったです。

Keynoteで出ていた今年の規模感がこちらです。

![Data + AI Summit 2026 の規模を示すスライド。total attendees 100K、174+ countries represented、800+ sessions and trainings、240+ exhibitors、350+ customer sessions](/images/dais-2026/event_metrix.png)
*参加者はオンライン含めて10万人、現地は174カ国から31,309名。セッション・トレーニングは800以上、うち顧客事例セッションが350以上を占めていました。*

今回は私は初参加で、Databricksがこの先どこへ向かうのか、他の企業がどうDatabricksを活用しているのか、現地で自分の目で確かめたいと思い参加しました。

## Keynoteの全体感

Keynote全体で繰り返し語られていたのは「**AIに足りないのは知能ではなく、コンテキストだ**」というメッセージでした。CEOのAli Ghodsiは「十分に賢いAIはもう誰でも使える状態だ。それでも企業で活用が進まないのは、データにコンテキストが与えられていないからだ」と話していました。今年の軸は「AIの性能」から「**AIを信頼してビジネスで使えるか**」へ移ったと感じます。

それを象徴していたのが、このスライドです。

![Keynote のスライド。「AGI is here today.」に取り消し線が引かれ、その下に「Just not at work.」とある](/images/dais-2026/AGI.png)
*「AGIはもう来ている。ただし、仕事の現場ではまだだ」。知能はもう足りている、足りないのは仕事で使うための土台だ、というメッセージでした。*

その課題をDatabricksはContext / Cost / Control / Choiceの4つに整理していました。

- **Context（文脈）**: データに業務の意味を与え、AIに正しく理解させる
- **Control（コントロール）**: Unity CatalogやAI Gatewayによるガバナンス・セキュリティ
- **Cost（コスト）**: 従量課金が前提の時代に、コスト管理が必要不可欠
- **Choice（選択）**: フロンティアモデルも汎用モデルも自由に選べるオープンプラットフォーム

![Databricks Data + AI Platform の4つの軸を示すスライド。Context、Control、Cost、Choice の4つのアイコンが並ぶ](/images/dais-2026/4c.png)
*この4つのCが、今年の発表全体を貫く軸になっていました。*

このどれかに対応して、機能の紹介をしていました。


### 全体を通して感じた、いちばん伝えたいこと

今回のSummitで感じたことは、一貫していました。

**AI時代の勝者は、派手なAIアプリを作る企業ではなく、AIが正しく働くためのデータと業務の土台を握る企業になる。その土台をどれだけ作り込めるかが、これからの肝になってきます。** モデルはもう十分に賢く、差がつくのは、そのモデルにどれだけ自社のコンテキストを正しく渡せるかどうかです。

今は、テーブルを渡せばAIはSQLを書いてくれます。しかし「売り上げはどこまでを含むのか」「ID同士をどう紐づけるのか」といった業務文脈がないと、AIはもっともらしいけれどズレた答えを出してしまいます。だからこそ、コンテキストと業務理解を基盤に持たせ、AIへ正しく渡せることが重要になってきます。

その結果、データ基盤そのものの役割も変わってきています。これまでは「データを貯めて、人が見に行く」基盤でした。それが今は、「**AIがビジネスで安心・安全に意思決定するための基盤**」へと変わろうとしています。今回の発表は、その変化をDatabricksがプラットフォーム全体で示したものだと受け取りました。

実際、発表された機能もこれに沿って並んでいました。Genie OntologyはContextをそのまま製品化したもの、Unity AI GatewayはCost、LakewatchはControl、というように4つのCに対応づけて紹介されていたのが印象的です。また、800以上のセッションのうち350以上が顧客事例で、そこで共通して語られていたのは「データ量ではなく、データの意味づけとビジネス部門との連携が成否を分ける」という点でした。どれだけコンテキストを基盤に組み込めるかが、鍵になってきます。

さらに、全体を貫いていたのが「**Lakehouseはエージェント時代のOS**」という位置づけです。これまで「データウェアハウスの代替」だったDatabricksが、今回は守備範囲を一気に広げてきました。OLTP（Lakebase）・リアルタイム分析（Lakehouse//RT）・エージェント開発（Agent Bricks）・アプリ実行基盤（Apps）・マーケティング（CustomerLake）まで、すべてをUnity Catalogのガバナンス配下に統合してきたのです。「Databricksの中で完結させる」という方針を、本気で進めている印象でした。

Keynoteはオンラインでも見られます。雰囲気だけでも伝わると思うので、貼っておきます。

Day 1:

https://www.youtube.com/watch?v=Qux8E-L1mk8

Day 2:

https://www.youtube.com/watch?v=sn9My5Pj0mE

## 発表された新機能

ここからは、今回発表された機能を紹介します。
下の図のとおり数がとても多く、Databricksの勢いを感じますね。
機能ごとに簡単な解説と、自分が感じたことを軽く添えていきます。
![今回発表された機能の全体像を示すスライド](/images/dais-2026/overall_2.png)

### Genie Ontology：データの意味と関係性を自動でナレッジグラフにする
今回の発表はGenie関連が多く、Databricksのあらゆる機能をGenieを軸にリブランディングしていました。
その流れの中で、Contextの文脈で発表されたのがGenie Ontologyです。
Genie Ontologyは、テーブル・クエリ・ダッシュボード・パイプライン・アプリといった社内の資産から、組織の概念や指標、それらの関係性を自動で抽出してナレッジグラフにします。

おもしろいのが **OntRank** という仕組みで、同じ指標でも「定義の作成者の権威」「参照される頻度」「認定済み資産との近さ」「新しさ」を見て、どのデータソースが信頼できるかを自動で判定します。

![Genie Ontology の仕組みを示すスライド](/images/dais-2026/genie_ontology.png)

今まではコンテキストがなかったため、AIにテーブルを渡すだけでは回答が不十分で、結局使われないことがありました。
この「意味を渡す層」を製品に組み込んだことで、回答精度が上がり、AI単独でも意思決定しやすくなったのかなと思います。4つのCでいうContextそのものを製品化したのが、まさにこのGenie Ontologyです。

参考記事は以下です。
https://www.databricks.com/jp/blog/introducing-genie-one-genie-ontology-and-genie-agents


### Genie One：自然言語でデータに質問できる「AIの同僚」
Genie Oneは、SlackやTeams、モバイルアプリから自然言語でデータに問い合わせられるサービスです。回答はすべてUnity Catalogのアクセス権限に沿うので、見ていい人にしか見えない形でガバナンスが効きます。MCPサーバー経由で他のエージェントから呼び出せるのもポイントです。

![Genie One の画面](/images/dais-2026/genie_one.png)

Keynoteの場でモバイルアプリのリリースが発表されたのにはびっくりしましたし、使えるようになったのは感動的でした。自然言語での問い合わせだけでなく、Databricks Appsも使えるので、営業の外出先でもGenie Oneを通して、その場で分析したり画面を見せたりしながら商談できます。大きな体験のアップデートだと感じました。

![Genie One のモバイルアプリ画面](/images/dais-2026/genie_one_app.png)

参考記事は以下です。
https://www.databricks.com/jp/blog/introducing-genie-one-genie-ontology-and-genie-agents

### Genie Agents：業務特化のエージェントを会話で作れる
Genie Agentsは、特定の業務に特化したエージェントを自分で作れる仕組みです（旧称はGenie Space）。プロンプトを1つ書くだけでエージェントが立ち上がり、人が逐一指示しなくても複数ステップの作業を自律的に進めてくれます。構造化データだけでなく、ドキュメントやファイルといった非構造化の情報源も一緒に扱えるのが特徴です。

汎用的に何でも答えてくれるGenie Oneに対して、Genie Agentsは「この業務専用の担当者」を量産するイメージをもちました。現場ごとに欲しいエージェントを、専門知識がなくても会話から組み立てられるのは手軽で良いなと感じました。

![Genie Agents の画面](/images/dais-2026/genie_agents.png)

参考記事は以下です。
https://www.databricks.com/jp/blog/introducing-genie-one-genie-ontology-and-genie-agents

### Genie Code：データ／ML開発に特化したコーディングエージェント
Genie Codeは、データエンジニアリングやMLに特化したコーディングエージェントです。スクリプトやパイプラインの中身を理解した上で、ノートブック・パイプライン・ダッシュボード・MLflowのコードの生成・評価・デプロイまでを自律的にやります。Keynoteでは「社内パイプラインの60%を3か月で生成した」「Genie Ontologyを活用すると汎用エージェント比で2.4倍の成功率」といった話が出ていました。MCP（外部ツール連携）もAgent Skills（ドメイン固有の手順定義）も備えています。

今までローカルのClaude Codeでパイプライン開発や分析をしていたので、これを機に試してみたいなと思いました。

![Genie Code の画面](/images/dais-2026/genie_code.jpeg)

参考記事は以下です。
https://www.databricks.com/jp/blog/introducing-genie-code

### Genie ZeroOps：パイプライン障害の検知から修正まで自動化
Genie ZeroOpsは、ジョブ・パイプライン・テーブル・MLワークロードの運用を自動化するエージェントです。目に見えるエラーが起きる前に、データ品質の指標の変化から異常を検知し、Unity Catalogのリネージをたどって原因を特定します。そのうえでGenie Codeと連携し、GitHubのPRやJiraチケットといった開発の文脈も踏まえて修正コードを生成する、という構成でした。

とくに良いと感じたのが、いきなり本番にデプロイしない設計になっている点です。本番データそのものは複製せず、元データを参照する隔離環境を作ってそこで検証するので、ユーザーの承認なしに本番へ反映されることはありません。

![Genie ZeroOps の画面](/images/dais-2026/genie_zeroops_2.png)

参考記事は以下です。
https://www.databricks.com/jp/blog/introducing-genie-zeroops

### Genie App Builder：自然言語だけで社内アプリを作れる
Genie App Builderは、作りたいものを自然な言葉で説明するだけで社内アプリを作れるツールです。リアルタイムでプレビューを見ながら反復して仕上げられて、エンジニアでなくても使えます。Unity Catalogの権限やガバナンスを保ったままアプリを生成してくれるのもポイントです。

![Genie App Builder の画面](/images/dais-2026/genie_app_bulder.png)

今まではClaude CodeでDatabricks Appを開発していましたが、これがDatabricks上で完結するようになりました。Claude Codeがなくても手軽に業務アプリを作れるのは良いですね。

参考記事は以下です。
https://www.databricks.com/jp/blog/enabling-governed-vibe-coding-enterprise-apps-databricks

### Agent Bricks：エージェントを安全に作って運用する開発者向け基盤
Agent Bricksは、エージェントを構築・デプロイ・運用するための開発者向け機能です。主要モデルにネイティブにアクセスでき、Unity CatalogのMCPサポートでGoogle DriveやJira、Slack、GitHubといった外部ソースにも安全につなげることができます。Unity AI Gatewayでガバナンスとコストを効かせ、Databricks Sandboxの隔離環境で安全に動かせるのも特徴です。

![Agent Bricks の構成図](/images/dais-2026/agent_bricks.png)

社内でエージェントを増やしていくなら、こういう土台に乗せて作るほうがラクになってくるだろうなと感じました。Agent Bricksは東京リージョンでも使えます。Cross-Geo Routingを有効にする必要はありますが、さっそく試してみたいですね。

参考記事は以下です。
https://www.databricks.com/jp/blog/agent-bricks-dais-2026

### Agent Memory Service：エージェントに過去のやり取りや知見を記憶させる
Agent Memory Serviceは、エージェントに過去のことを覚えさせておく機能です。やり取りの履歴そのものに加えて、そこから学びとった知見や知識もLakebaseにためていけます。覚えた内容は本人だけが使うものとチームで共有するものを分けられて、共有する場合もアクセス権の範囲に収まります。

![Agent Memory Service の画面](/images/dais-2026/agent_memory_services.png)

エージェントが毎回ゼロから始まらず、過去のやり取りを覚えていてくれるのは体験として大きいですね。一方で、個人情報や機密を記憶させるなら、保存範囲や保持期間は事前に整備しておく必要があるなと感じました。

参考記事は以下です。
https://www.databricks.com/blog/memory-scaling-ai-agents

### Databricks Sandbox：エージェントがコードを安全に試せる隔離環境
Databricks Sandboxは、エージェントが安全にコードを実行できる隔離環境です。VMで実行環境を隔離したうえで、データへのアクセス範囲もスコープを絞れるので、エージェントが機密データに触れたり本番のテーブルを壊したりするリスクを抑えられます。起動も1秒ほどと速く、サブエージェントや実験用にサッと立てて使い捨てにできます。

![Databricks Sandbox の画面](/images/dais-2026/agent_sandbox.png)

エージェントを自由に暴れさせて色々試したいけれど、本番に影響したら怖いという課題を解決してくれるのがこの機能だと感じました。隔離された環境で好きなだけ動かして、ダメなら捨てればいいので、安心してエージェントに任せられるのが良く、個人的に興味をもった機能のひとつでした。

参考記事は以下です。
https://docs.databricks.com/aws/en/compute/serverless/sandbox

### Unity AI Gateway：LLMのコストとアクセスを一元管理するゲートウェイ
Unity AI Gatewayは、LLMのコスト・ルーティング・トレーシングを一箇所に集めて管理する機能です。リクエストごとにトークン数だけでなくDBU換算のコストもUnity Catalogに記録され、どのモデルやプロバイダーにコストがかかっているかまで把握できます。予算はユーザー・ユースケース・ワークスペース・アカウントといった単位で設定でき、上限に近づくとアラートを出してくれます。

AliがKeynoteで何度もAIのコストの大きさに言及していたのが印象的でした。AIを使う機会が増えてきた今、思わぬコスト増で「AI破産」しないよう、そして安全に使えるよう、こうした基盤を早めに整えておくべきだと痛感しました。

![Unity AI Gateway の画面](/images/dais-2026/unigy_ai_gateway.png)

参考記事は以下です。
https://www.databricks.com/blog/introducing-ai-spend-controls-unity-ai-gateway

### Omnigent（OSS）：複数のコーディングエージェントを束ねるメタハーネス
Omnigentは、複数のコーディングエージェントの上に立つ「メタハーネス」です。skillやclaude.mdなどの設定ファイルをハーネス間で自動同期でき、同じskillをClaude CodeでもCodexでもそのまま動かせます。エージェントの切り替えは1行の変更で済み、ポリシーやコスト管理もOmnigentのレイヤーで一元的に効かせられます。セッション共有によるチームでの共同作業にも対応し、Apache 2.0ライセンスでオープンソース化されました。

![Omnigent のイメージ](/images/dais-2026/omnigent.png)

自分は生活圏をすべてClaude Codeに集約していて、skillやMCP、memoryをふんだんに使っています。そのため、なかなか他のツールへ乗り換えられずにいましたが、Omnigentならこの資産を活かしたまま別のエージェントでも動かせそうなので、一度試してみたいですね。また、OSSなので、自分もコミットして貢献できればと思っています。

参考記事は以下です。
https://www.databricks.com/jp/blog/introducing-omnigent-meta-harness-combine-control-and-share-your-agents

### LTAP / Lakebase：OLTPとOLAPをコピーなしで1つに統合
LTAP（Lake Transactional/Analytical Processing）は、トランザクション処理（OLTP）と分析処理（OLAP）を、レイク上の単一のデータコピーで統合するアーキテクチャです。データを複製せず、ワークロードごとにコンピュートを分離でき、CDCも挟まないので常に最新のデータを扱えます。今まで別々のシステムに分かれていた処理を、ETLなしで1つにまとめられるわけです。

![LTAP（Lake Transactional / Analytical Processing）。トランザクション用と分析用の2つのコンピュートが、レイク上の1つのデータコピーを共有する](/images/dais-2026/ltap_2.png)

![LTAPの仕組み。OLTPの書き込み（Lakebase Compute）とOLAPの分析クエリ（Lakehouse Compute）が、レイク上の同じデータを共有する](/images/dais-2026/ltap_4.png)

そのOLTP側を担うのが、Postgres互換のサーバーレスDB、Lakebaseです。利用しないときはゼロまで縮むオートスケール、別リージョン・別クラウドへフェイルオーバーできるマルチクラウド対応、そして500ミリ秒以下で本番データのブランチを切れるのが特徴です。

本番のコピーをブランチとして切って試し、問題なければ反映する、という開発体験がDB側でも得られるのは地味にうれしいポイントでした。個人的にずっと待ち望んでいた機能で、なんとこの記事を書いている今日、日本リージョンでも使えるようになりました。さっそく触ってみようと思います。

参考記事は以下です。
https://www.databricks.com/company/newsroom/press-releases/databricks-launches-ltap-first-lake-transactionalanalytical

### Lakebase Search：Postgresに全文＋ベクトルのハイブリッド検索を内蔵
Lakebase Searchは、Lakebaseに組み込まれた、エージェント向けの検索機能です。ベクトル検索と全文検索（BM25）を1つのSQLで組み合わせたハイブリッド検索ができ、別途ベクトルDBを用意する必要がありません。

![「fast sports car」というクエリに対する、全文検索（lakebase_text・BM25）とベクトル検索（lakebase_vector・ANN）の違い。全文検索は語が一致する文書だけを返し、ベクトル検索は意味の近い文書を返す。ハイブリッド検索は両者をマージする](/images/dais-2026/lakebase_search_keyword_vs_vector.png)
*全文検索（BM25）は「red sports car」のように語が一致する文書だけを拾い、「quick automobile」「Ferrari 488 supercar」のような言い換えは取りこぼす。ベクトル検索は意味の近さで拾う。Lakebase Searchはこの両方を1つのSQLで組み合わせられる（[出典: Databricks公式ドキュメント](https://docs.databricks.com/aws/en/oltp/projects/lakebase-search)）。*

個人的には全文検索をずっと求めていたので、まさに待望のアップデートでした。使えるようになるのが楽しみです！

参考記事は以下です。
https://www.databricks.com/blog/announcing-lakebase-search-agent-native-retrieval-built-lakebase-postgres

### Lakehouse//RT：大量のクエリをレイクハウス上で直接ミリ秒級に返すリアルタイム分析
Lakehouse//RTは、Reydenという新エンジンによるリアルタイム分析機能です。レイクハウス上で直接ミリ秒級の応答を返せます。デモでは高い同時実行のもとでも低レイテンシを維持していました。現時点ではBetaで、読み取り専用です。

![Lakehouse//RT のデモ](/images/dais-2026/lakehouse_rt.png)

全社員がよく見るダッシュボードを速くしたいニーズはあるので、効きそうだと感じました。そして、デモでの速さにはかなり感動したので、ぜひ見てください。

https://x.com/databricks/status/2066933185238257768

参考記事は以下です。
https://www.databricks.com/jp/blog/introducing-lakehousert-real-time-performance-unified-lakehouse

### OpenSharing：環境を問わずデータ・モデル・スキルを共有するプロトコル
OpenSharingは、Delta Sharingの後継となるオープンなデータ共有プロトコルです。テーブルだけでなくAIモデルやエージェントスキルまで、相手の環境を問わず共有でき、Linux Foundationのオープンソースプロジェクトにもなりました。

![OpenSharing の画面](/images/dais-2026/opensharing.png)

個人的にいちばん感動したのが、オンプレミスのデータもそのまま共有できる点です。置き場所も相手の環境も問わずにシェアできるのは、これまでのデータ共有の制約を一気に取り払ってくれるなと感じました。

参考記事は以下です。
https://www.databricks.com/company/newsroom/press-releases/databricks-announces-opensharing

### Lakeflow Designer：ノーコードでETLパイプラインを組む
Lakeflow Designerは、ノーコードでETLパイプラインを組める機能です。UIと自然言語の両方で構築でき、非データエンジニアでも簡単にパイプラインを作れます。生成されるのは宣言的なパイプラインで、Unity Catalogのガバナンス下に置かれ、バージョン管理や監視も効いた本番対応の状態で出てきます。

![Lakeflow Designer の画面](/images/dais-2026/lakeflow_designer.png)

今はdbt + Databricksでの構成なので、どう使い分けるか・どこを置き換えられるかは、これから触って見極めたいところです。正直、今まで自分でコードを書いてきたぶん少し抵抗もありますが、アンラーニングして触ってみたいですね（笑）。

参考記事は以下です。
https://www.databricks.com/blog/announcing-lakeflow-designer-no-code-etl

### AI Runtime（Serverless GPU）：GPUをサーバーレスで使えるML学習環境
AI Runtimeは、ディープラーニングやLLMのトレーニング・ファインチューニングを動かすサーバーレスなトレーニング環境です。クラスターを立てずに、ノートブックから数クリックでA10やH100のGPUを使えます。PyTorchやCUDAも最初から入っているのですぐ学習を始められ、課金も使った分だけ。今回はマルチノードトレーニング対応も発表されました。

![Serverless GPU の画面](/images/dais-2026/serverless_gpu.png)

これまでGPUの環境構築が大変でしたが、数クリックで学習を始められるのは簡単に試せていいですね。

参考記事は以下です。
https://www.databricks.com/blog/introducing-ai-runtime-scalable-serverless-nvidia-gpus-databricks-training-and-finetuning

### App Spaces：複数アプリをまとめてガバナンスする
App Spacesは、関連する複数のアプリをひとまとめにして統治する機能です。認証・共有範囲・予算・外部ネットワークアクセスといったポリシーをスペース単位で定義しておけば、その中のアプリすべてに適用され、利用状況やコストもまとめて追跡できます。アプリが増えても1つずつ設定する手間がなくなります。

![App Spacesのスライド。Workspace AdminがSpace-level Policies（ワークスペース/Unity Catalog・認証・共有・予算・外部ネットワーク）を定義し、その配下でApp BuilderがデプロイしApp Userが使う。利点はMulti-app policies・Governed sharing・Scalable operationsの3つ](/images/dais-2026/app_spaces.png)

今までアプリを1つずつ管理していたので、まとまった単位で管理できるようになると運用が楽になって嬉しいです。


参考記事は以下です。
https://www.databricks.com/blog/enabling-governed-vibe-coding-enterprise-apps-databricks

### Serverless Micro Apps：アプリを必要なときだけ動かす
Serverless Micro Appsは、小さなアプリを必要なときだけ立ち上げて安価に動かせる実行環境です。従来のDatabricks Appsはずっと起動しっぱなしでコストがかかっていましたが、Micro Appsはアイドル時にゼロまでスケールダウンするので、コストを最適化できます。

![Serverless Micro Apps のイメージ](/images/dais-2026/sercerless_micro_apps.png)

この機能は、Databricks Appsが出たときからずっと待っていました。これまでは自前で自動停止のジョブを組んで運用していたのですが、その役目もこれで終わりですね。

参考記事は以下です。
https://www.databricks.com/blog/enabling-governed-vibe-coding-enterprise-apps-databricks

### Lakewatch：エージェントが脅威検知から対応まで回すSIEM
Lakewatchは、エージェント型のSIEMです。セキュリティ・IT・業務のデータをUnity Catalogの上に集め、防御側のエージェントが脅威の検知から対応までを自動化します。Genieで脅威の調査を自然言語で回せて、既存SIEMの弱点だったデータ量・コスト（最大80%削減）・非構造化データへの対応も解消します。Pantherの買収と合わせて発表され、「セキュリティレイクハウス」という方向を打ち出していました。

![Lakewatch のイメージ](/images/dais-2026/lakewatch.png)

AIが攻撃側にも使われて攻撃の速度と規模が上がっている今、守る側も人手では追いつかないので、エージェントで防御していかなければと感じています。データは重要な資源なので、Databricksがセキュリティ領域まで踏み込んできたのはとても良い流れですね。

参考記事は以下です。
https://www.databricks.com/blog/databricks-announces-lakewatch-new-agentic-siem

### CustomerLake：エージェント型のCustomer Data Platform（CDP）
CustomerLakeは、Databricksが出したCustomer Data Platform（CDP）です。CDP自体は以前から各社がリリースしていて、色々なチャネルに散らばった顧客データを1つに集め、マーケティング施策に使えるようにする基盤のことです。CustomerLakeは、そこにエージェントを組み込んだのが新しいところです。

機能をもう少し具体的に見ると、Profile Agentが生の顧客データを一人ひとりのプロファイルに整え、Campaign Agentがその人のシグナルから「次に取るべきアクション」を決めて、メールや広告などチャネルをまたいで実行します。今までのように「セグメントを切ってキャンペーンを打つ」のではなく、個人単位で1:1の判断を回し続けるのが特徴です。

![CustomerLake の全体像。Data Ingestionから、Customer 360やIdentity Resolutionを担うProfile Agents、オーディエンス生成やパーソナライズを担うCampaign Agentsを経て、Reverse ETLで各ツールへ書き戻すまでをカバーする](/images/dais-2026/cdp_2.png)
*Profile AgentsとCampaign Agentsの2系統で、データ取り込みから施策の実行・書き戻しまでを一気通貫で担う。下支えするのが、個人単位で動き続けるInfinity Campaignsという構成。*

特徴的だと感じたのは、次の3つです。

**Identity Resolution（名寄せ）**: 別々のデータソースに散らばった同じ顧客を1つにまとめる仕組みです。Profile Agentが担い、LLMベースのエージェントがルールを学習・進化させるので、従来のルールベースやMLより高精度だと説明されていました。ID統合はCDPでよくある課題で、同じ人が別レコードのままだと適切にアプローチできません。ここを自動で詰められるのは大きいです。

![1st Party Agentic Identity Resolution のスライド。Agent-generated rules → LLM-based exception handling → Human feedback の3段フォールバックと、Agentic Learning Loop。IDRとは「共有キーなしで、すでに持っているデータ間で同じ顧客を紐づけること」と説明されている](/images/dais-2026/cdp_9_idr.png)
*エージェントが生成したルールで名寄せし、例外はLLMが処理、最後に人がフィードバックする。その結果が学習ループに戻る構成。共有キーがなくても同一顧客を紐づけられるのがポイント。*

**Reverse ETL**: 作成した顧客リストなどのオーディエンスを、コネクタで販売・マーケのツール側へ1クリックで書き戻せます。年内にさらにコネクタを追加予定とのこと。基盤に貯めたデータを、使う側のツールに戻すところまで面倒を見てくれます。

![Managed Reverse ETL Connectors のスライド。Lakeflow Connectがバックエンド。Adobe・Braze・Iterable・Marketo・HubSpotなどのマーケ系と、Meta・Google Ads・TikTok・Amazonなどの広告系コネクタが並び、「More connectors coming soon」とある](/images/dais-2026/cdp_4_reverse_etl.png)
*書き戻し先はマーケ・エンゲージメント系と広告プラットフォーム系に広く対応。Lakeflow Connectが裏側を担っている。*

**Clean Room（クリーンルーム）**: オーディエンスをクリーンルーム経由で配信し、プライバシーを保ったままパートナーとデータを共有できます。

![Identity Resolution in Clean Rooms のスライド。LiveRamp・Acxiom・Epsilon・TransUnion・adstra・The Trade Deskなどをローンチパートナーに、自社の顧客データをサードパーティのIdentity Graphへセキュアにマッピングできる](/images/dais-2026/cdp_8_clean_room.png)
*クリーンルーム内で、外部のIdentity Graphと安全に突合する。生データを相手に渡さずに名寄せできるのが、プライバシー規制が厳しくなる時代に効いてくる。*

正直、これを聞いたときはドキッとしました。Databricksのような大きな企業がCDPの領域まで侵食してくると、自社のサービスが食われてしまわないか、という不安がよぎりました。「コミュニケーションデータから、業務で使われる次のアクションを作る」という方向は、自分たちが目指しているものと重なる部分があるからです。ただ、Databricksが同じ問題に乗り出してきたのは、裏を返せば市場がある証拠でもあります。自分たちはどこで勝つのか、自社の提供サービスを改めて見つめ直すきっかけになりました。

参考記事は以下です。
https://www.databricks.com/blog/introducing-customerlake-agentic-cdp

## 実際に現地へ行ってみた感想

ここからは、現地で感じたことを書いていきます。

まず驚いたのが、街ぐるみでData + AI Summitを知っていたことです。入国審査でも空港からのタクシーでも「Databricksのイベントに行くんだね」と言われ、街全体がこのカンファレンスを知っている空気でした。会場のまわりもDatabricks一色にジャックされていて、着いた瞬間からテンションが上がりました。

![会場周辺がDatabricksの装飾でジャックされている様子](/images/dais-2026/dais.jpg)

会場はKeynoteの開演前から長蛇の列、人気セッションの予約はほぼ埋まっていて、立ち見も出ていました。そして最後は、ORACLE PARKを貸し切ってのライブです。The Chainsmokersまで来ていて、「Databricksお金かけてるなー」とびっくりしました！

![ORACLE PARKを貸し切ったクロージングライブ](/images/dais-2026/live.jpg)

EXPOではちょうどワールドカップ期間と重なっていて、パブリックビューイングのブースがいちばん盛り上がっていたかもしれません（笑）。

![EXPO内のワールドカップ観戦ブース](/images/dais-2026/worldcup.jpg)

技術とは何の関係もないのですが、アメリカはとにかくなんでもデカい。ステーキを頼んだら皿からはみ出る量が出てきて、案の定食べきれませんでした。

![皿からはみ出るアメリカンサイズのステーキ](/images/dais-2026/steak.jpg)

街中ではWaymoが普通に走っていて、念願の自動運転にも乗れました。ハンドルがひとりでに動くのを後部座席から眺めるのは、最初こそ少し怖かったものの、途中からは慣れて、未来を感じてワクワクしました。

![サンフランシスコ市内を走るWaymoの自動運転車](/images/dais-2026/waymo.jpg)

### 現地で聞くと、記憶への残り方が違う

現地に行かなくても、Keynoteはオンラインで見られます。それでも、生の発表をその場の熱狂の中で聞く体験は大きいなと感じました。リアルに味わったワクワクは記憶に残りやすく、「帰ったら絶対に試すぞ」というモチベーションも湧いてきます。このライブ感は配信では得られないもので、自分にとって貴重な経験になりました。

### 現地でしか得られない情報がある

セッションやブースで得られる情報は、現地でしか手に入りません。今回、私はIVRyのData Hubに近いデータ基盤が海外の事例ではどう動いているのかが気になっていて、それを中心に聞いて回りました。「こうやってDatabricksを取り入れている」「ここで困っている」といった話を現地の人と直接交わせたのは、大きな収穫でした。

### 日本人同士のつながりが強くなった

海外に行くと、見知らぬ人でも日本人同士はとても話しやすいです。同じDatabricksの発表を聞き、同じ熱狂を共有しているので、それだけで一気に距離が縮まりました。同じイベントに参加して、同じものに興味を持っている人同士ならではの縮まり方ですね。ちょうどワールドカップをやっていたのも、盛り上がった理由かもしれません。

## おわりに

自分にとっては念願の、はじめての海外出張でした。とても刺激的で、学びも多かったです。イベントでお会いした人もみんな口を揃えて「日本へ帰ったら早く触りたい」と言っていて、これは現地に来たからこそ味わえる熱量だと思います。同じ場で同じ発表を聞いて熱狂を共有できたことが、いちばんの収穫でした。

そして何より、決して安くない渡航費のなか快く送り出してくれた会社とチームのメンバーには、感謝しかありません。帰ったら1つずつ試してみんなに共有し、IVRyのデータ基盤を「AIをビジネスで使うための意思決定基盤」に近づけていければと思っています。来年は僕でなくても、誰かは必ず現地に行ったほうがいいと断言できるイベントでした。

今回はKeynote中心のまとめでしたが、参加したセッションのレポートも追ってblogに上げる予定です。ここで紹介した機能も実際に使ってみて、その様子をまたまとめられたらと思います。

そして次回は、必ずビリヤードをプレイしてきます！
