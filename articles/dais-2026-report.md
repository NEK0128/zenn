---
title: "Data + AI Summit 2026 現地参加レポート：ビリヤードボールと4つのCを持ち帰った"
emoji: "🎱"
type: "idea"
topics: ["Databricks", "DataAISummit", "AI", "データエンジニアリング", "IVRy"]
published: false
publication_name: "ivry"
---

こんにちは、IVRyでデータエンジニアとして働いている松田健司（[@ken_3ba](https://x.com/ken_3ba)）です。趣味はビリヤードで、プロの試合にも出ているぐらい割とガチでやっています。

今回は Data + AI Summit（以下 Summit）に参加してきたので、現地の様子と注目した発表をまとめます。

その前に、ビリヤードの話だけさせてください。Summit には会場のクエストがあって、達成するとビリヤードボールがもらえます。ビリヤードプレイヤーとしての使命感で集めてきました。

https://x.com/Data_AI_Summit/status/1933245536389198198

https://x.com/ken_3ba/status/2067036954949239180

ちなみに、会場の近くに本物のビリヤード場もあったみたいですが、行けなかったのが心残りです。それでは本題に入ります。

## TL;DR

- Databricks の通底メッセージは「**AGI はもう来ている。問題は知能ではなく、その周りの Context / Cost / Control / Choice だ**」。Lakehouse を「エージェント時代の OS」と位置づけ、OLTP からマーケまで Unity Catalog 配下に垂直統合してきた。
- 発表の中心は **Genie One / Ontology、Unity AI Gateway、Agent Bricks、LTAP・Lakebase、CustomerLake**。Databricks は「AI がデータを理解し意思決定する基盤」に振り切っていた。
- そして何より、現地に行く価値が大きい。発表をオンラインで見るのと、現場の熱狂の中で聞くのとでは、記憶への残り方も「帰ったら試すぞ」というモチベーションも全然違う。

## IVRy と Databricks

IVRy は2025年7月、データ・AI 活用を加速させる新基盤として、Databricks を中心としたプラットフォームを構築しました。データが増えるにつれて転送コストが膨らんだり、データと権限があちこちに散らばったりという課題があり、それらを一元管理できてクラウド内で完結し、AI 開発もしやすい基盤として Databricks を選んだ、という背景があります。

通話やメールなどのコミュニケーションデータを AI が扱える形に変える「IVRy Data Hub」というプロダクトも、この Databricks 基盤の上で動いています。

https://ivry.jp/function/datahub/

導入の背景や採用理由は、登壇資料が詳しいので、こちらをどうぞ。

https://speakerdeck.com/keisukeosone/gendatoivry-ji-cheng-chang-sutatoatupugadatabrickswocai-yong-sitali-you-data-plus-ai-world-tour-2025

## Databricks Data + AI Summit とは

Data + AI Summit は、Databricks が年に一度開催する世界最大級のデータ・AI のカンファレンスです。毎年、サンフランシスコの Moscone Center で開催されています。今年は6月15日から18日まででした。

https://www.databricks.com/dataaisummit

前回は IVRy の別のメンバーが参加していたのですが、その一人が「今年は規模が明らかに大きくなっていた」と言っていました。公式発表によると、現地参加は174カ国から31,309名。日本からも500名くらいは来ていたと聞きました。そして何より、今回も発表される機能がとても多かったです。

Databricks がこの先どこへ向かうのか、導入して一年ほどの自分の目で確かめたい。それが今回参加した一番の動機でした。

## Keynote の全体感

Keynote を貫いていたのは「**AI に足りないのは知能ではなく、コンテキストだ（AI doesn't have an intelligence problem, it has a context problem）**」というメッセージでした。CEO の Ali Ghodsi は「AGI はもう来ている」と言い切ったうえで、モデルはもう十分賢いのに AI が業務で使われないのは、それを取り巻く課題が解けていないからだ、と話していました。

その課題が Context / Cost / Control / Choice の4つです。組織のデータや業務を AI に理解させる Context、青天井になりがちな AI コストを抑える Cost、エージェントの暴走や情報漏えいを防ぐ Control、特定モデルやクラウドへのロックインを避ける Choice。発表された機能は、だいたいこのどれかに対応していました。

<!-- ここに画像を入れる: Context / Cost / Control / Choice の整理スライド -->

この整理は、自分の感覚とも合っていました。私はもともと、Databricks はもう単なるデータ基盤ではなく、AI が業務を理解して意思決定するための基盤に向かっていると考えていました。テーブルを渡せば AI は SQL を書ける。でも「売上に返品を含むのか」「どの顧客 ID と契約 ID を紐づけるのか」といった業務文脈がないと、AI はもっともらしいがズレた答えを出す。今回いちばん力が入っていた Genie Ontology はこの Context をそのまま製品にしたものですし、マーケティング領域に踏み込んだ CustomerLake は、意思決定の基盤というビジョンを業務アプリの側から見せたものに感じました。

そしてもう一つ、全体を貫いていたのが「**Lakehouse はエージェント時代の OS**」という位置づけです。これまで「データウェアハウスの代替」だった Databricks が、今回は OLTP（Lakebase）、リアルタイム分析（Lakehouse//RT）、ストリーミング取り込み（ZeroBus）、エージェント開発（Agent Bricks）、アプリ実行基盤（Apps）、マーケティング（CustomerLake）まで、すべてを Unity Catalog のガバナンス配下に統合してきました。「Databricks の中で完結させる」という設計を、本気で進めている印象でした。

Keynote はオンラインでも見られます。雰囲気だけでも伝わると思うので、貼っておきます。

https://www.youtube.com/watch?v=Qux8E-L1mk8

https://www.youtube.com/watch?v=sn9My5Pj0mE

## 発表された新機能

ここからは、今回発表された機能を一つずつ紹介していきます。数が多いので、機能ごとに簡単な解説と、自分が感じたことを軽く添える形でいきます。

### Genie One / Genie Ontology / Genie Code / Genie ZeroOps

https://www.databricks.com/jp/blog/introducing-genie-one-genie-ontology-and-genie-agents

Genie One は、Slack や Teams、モバイルアプリから自然言語でデータに問い合わせられる「AI の同僚」です。その裏側で効いているのが Genie Ontology で、テーブルやクエリ、ダッシュボードから組織の概念や指標、関係性を自動で抽出してグラフにします。OntRank という、Google の PageRank の発想を借りた仕組みで「どのデータソースが信頼できるか」まで判定してくれるのがおもしろい。ほかにも、ML 向けの Genie Code や、推論テーブルのデバッグを担う Genie ZeroOps が発表されていました。

テーブルを渡すだけでは AI は正しく答えられない。意味を渡す層を製品として持ってきたのは、素直にうらやましいと思いました。

<!-- ここに画像を入れる: Genie One / Genie Ontology の画面 or 概念図 -->

### Unity AI Gateway

https://www.databricks.com/blog/introducing-ai-spend-controls-unity-ai-gateway

LLM のコスト・ルーティング・トレーシングを一箇所に集めるレイヤーです。ユーザーやワークスペース、アカウント単位で予算アラートを出したり、上限に達したらリクエストを自動で止めるハードキャップを設定できます。

Ghodsi が Keynote で「これはものすごく高くつくぞ」とはっきり言っていたのが印象的でした。AI を本番で叩く機会が増えてきた今、チーム単位の予算上限とトレースを早めに入れておくべきだと痛感しました。

<!-- ここに画像を入れる: Unity AI Gateway の予算管理画面 -->

### Agent Bricks

https://www.databricks.com/jp/blog/agent-bricks-dais-2026

エージェントを安全に作って運用するための基盤です。Unity Catalog に MCP（Model Context Protocol）サポートが入り、Google Drive や JIRA、Slack、GitHub といった外部ソースへ安全につなげるようになりました。セキュリティポリシーや予算管理を組み込んだまま動かせます。

社内でエージェントを増やしていくなら、最初からこういう土台に乗せて作るほうが結局ラクだろうな、と思いました。

<!-- ここに画像を入れる: Agent Bricks の構成図 or 画面 -->

### LTAP / Lakebase

https://www.databricks.com/company/newsroom/press-releases/databricks-launches-ltap-first-lake-transactionalanalytical

LTAP（Lake Transactional/Analytical Processing）は、トランザクション処理（OLTP）と分析処理（OLAP）を、レイク上の単一のデータコピーで統合するアーキテクチャです。40年間ずっと別々のシステムに分かれていた処理を、ETL や CDC を挟まずに一つにする、という話でした。その OLTP 側を担うのが、Postgres 互換のサーバーレス DB、Lakebase です。

Git 風のブランチング機能で本番データに対して安全に実験できるようになるのは、開発者として地味にうれしいポイントでした。Iceberg 統合と合わせて、特定の製品やクラウドに縛られない選択肢を取りやすくなる方向です。

<!-- ここに画像を入れる: LTAP のアーキテクチャ図 -->

### Lakehouse//RT

https://www.databricks.com/jp/blog/introducing-lakehousert-real-time-performance-unified-lakehouse

Reyden という新エンジンによるリアルタイム分析機能です。データを動かさずに、レイクハウス上で直接ミリ秒級の応答を返せる。デモでは高い同時実行のもとでも低レイテンシを維持していました。現時点では Beta で、読み取り専用です。

お客さん向けのダッシュボードを速くしたい場面で効きそうだと感じました。

<!-- ここに画像を入れる: Lakehouse//RT のデモ or ベンチマーク -->

### ZeroBus

ストリーミングのデータ取り込みを、Apache Kafka のようなメッセージバスを挟まずに、サーバーレスの push 型 API で直接 Delta テーブルに流せる仕組みです。間に挟むコンポーネントが減るので、構成がシンプルになります。

通話などのイベントをリアルタイムに集計したいとき、間の運用が減るのはありがたい。

<!-- ここに画像を入れる: ZeroBus の構成図 -->

### Lakeflow Designer

エージェント時代のデータエンジニアリングをうたう、パイプライン構築の機能です。dbt + Databricks で組んでいる今の構成と、どう棲み分くか・どこを置き換えられるかは、これから触って見極めたいところです。

<!-- ここに画像を入れる: Lakeflow Designer の画面 -->

### Omniagent（OSS）

Claude Code や Codex、Cursor といったエージェントを、統一インターフェースで切り替えながら使える OSS のハーネスです。自分は個人でも複数のツールを使い分けているので、これを組織単位でやれるのは気になりました。社内で一度試してみたいです。

<!-- ここに画像を入れる: Omniagent のイメージ -->

### Lakewatch（Panther 買収）

セキュリティ運用をエージェントのワークロードとして回す機能で、Panther の買収と合わせて発表されました。IVRy はまだ SIEM もセキュリティ運用チームも持っていないので今すぐの導入対象ではないですが、Databricks がセキュリティ領域まで内製してきたという流れは押さえておきたいところです。

<!-- ここに画像を入れる: Lakewatch のイメージ -->

### CustomerLake

https://www.databricks.com/blog/introducing-customerlake-agentic-cdp

Databricks がマーケティング領域に進出して出してきた、エージェント型の CDP（顧客データ基盤）です。Profile Agent が生の顧客データを Customer 360 プロファイルに整え、Campaign Agent が顧客のシグナルから「次の最適なアクション」を決めてチャネルをまたいで実行します。

正直に言うと、これを聞いたとき少しドキッとしました。「コミュニケーションデータから、業務で使われる次のアクションを作る」という方向は、自分たちが目指しているものと重なる部分があるからです。Databricks 公式が同じ問題に乗り出してきたのは、市場があることの証明でもあるし、同時に「お前たちはどこで勝つのか」を問われている気もしました。考え込みながら会場を出たのを覚えています。

<!-- ここに画像を入れる: CustomerLake の Profile Agent / Campaign Agent の図 -->

## 実際に現地へ行ってみた感想

ここからは技術の話をいったん離れて、現地で感じたことを書きます。

まず、街ぐるみで Summit を知っていたことに驚きました。入国審査でも、空港からのタクシーでも、「Databricks のイベントに行くんだね」と言われる。サンフランシスコの街全体がこのカンファレンスを知っている空気でした。

会場は Keynote の開演前から長蛇の列。人気セッションの予約はほぼ全部埋まっていて、立ち見も出ていました。日本人同士の交流会も開かれて、いろいろな人とつながれた。そして最後は ORACLE PARK を貸し切ってのライブです。The Chainsmokers まで来ていて、「カンファレンスの打ち上げってこんな規模でやるの？」と完全にビビりました。

あと、これは技術と何の関係もないのですが、アメリカはとにかくなんでもデカい。ステーキを頼んだら、皿からはみ出る量が出てきました。

<!-- ここに画像を入れる: ORACLE PARK のクロージングライブの写真 -->

<!-- ここに画像を入れる: アメリカのデカいステーキの写真 -->

### 現地で聞くと、記憶への残り方が違う

Keynote はオンラインでも見られます。それでも、現地で生の発表を聞いてワクワクして、その場の熱狂に飲まれてテンションが上がる、という体験は大きい。リアルな現場でワクワクした方が記憶に残りやすいし、「帰ったら絶対に試すぞ」というモチベーションが湧いてくる。このライブ感は、配信では得られないものでした。

### 現地でしか得られない情報がある

セッションやブースで得られる情報は、現地でしか手に入りません。今回、私は IVRy の Data Hub に近いデータ基盤が、海外の事例ではどう動いているのかが気になっていて、それを中心に聞いて回りました。「こうやって Databricks を取り入れている」「ここで困っている」といった話を現地の人と直接交わせたのは、とてもいい持ち帰りになりました。

### 日本人同士のつながりが強くなった

海外に行くと、日本人とはとても話しやすい。同じ Databricks の発表を聞いて、同じ熱狂を共有できる。それだけで一気に親密になれました。同じイベントに参加して、同じものに興味を持っている人同士ならではの距離の縮まり方です。ちょうどワールドカップをやっていたのも、盛り上がった理由かもしれません。

## おわりに

自分にとっては念願の初めての海外出張でした。とても刺激的で、学びも多かったです。一緒に行ったメンバーも口を揃えて「勉強意欲が上がった」と言っていて、これは現地に来たからこそだと思います。同じ場で同じ発表を聞いて熱狂を共有できたことが、いちばんの収穫でした。

そして何より、決して安くない渡航費のなか、快く送り出してくれた会社とチームのメンバーには感謝しかありません。持ち帰った宿題を、一つずつ手元で試していきます。来年も、誰かは必ず現地に行ったほうがいい。そう断言できるイベントでした。

次は、ビリヤードも打ってきます。
