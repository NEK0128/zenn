---
title: "Data + AI Summit 2026 現地参加レポート：ビリヤードボールと4つのCを持ち帰った"
emoji: "🎱"
type: "idea"
topics: ["Databricks", "DataAISummit", "AI", "データエンジニアリング", "IVRy"]
published: false
publication_name: "ivry"
---

Data + AI Summit（以下 Summit）に参加してきました。

まず最初に、毎年恒例のビリヤードボールです。会場のクエストを達成するともらえるので、ビリヤードプレイヤーとしての使命感で集めてきました。

https://x.com/Data_AI_Summit/status/1933245536389198198

https://x.com/ken_3ba/status/2067036954949239180

ちなみに、会場の近くに本物のビリヤード場もあったみたいですが、行けなかったのが心残りです。

## TL;DR

- Databricks の通底メッセージは「**AGI はもう来ている。問題は知能ではなく、その周りの4つの C（Context / Cost / Control / Choice）だ**」。Lakehouse を「エージェント時代の OS」と位置づけ、OLTP からマーケまで Unity Catalog 配下に垂直統合してきた。
- 注目の新機能は **Genie One / Ontology（Context）、Unity AI Gateway（Cost）、Agent Bricks（Control）、LTAP・Lakebase（Choice）、CustomerLake**。Databricks は「AI がデータを理解し意思決定する基盤」に振り切っていた。
- そして何より、現地に行く価値が大きい。発表をオンラインで見るのと、現場の熱狂の中で聞くのとでは、記憶への残り方も「帰ったら試すぞ」というモチベーションも全然違う。

## IVRy は Databricks を導入したばかり

私は IVRy でデータエンジニアをやっていて、Databricks を導入してからちょうど一年ほどになります。だからこそ、Databricks がこの先どこへ向かうのかを自分の目で確かめたくて、今回の Summit に参加しました。

通話やメールなどのコミュニケーションデータを AI が扱える形に変える「IVRy Data Hub」というプロダクトも、Databricks 基盤の上で動いています。

https://ivry.jp/function/datahub/

導入の背景や採用理由は、登壇資料が詳しいので、こちらをどうぞ。

https://speakerdeck.com/keisukeosone/gendatoivry-ji-cheng-chang-sutatoatupugadatabrickswocai-yong-sitali-you-data-plus-ai-world-tour-2025

## Databricks Data + AI Summit とは

Data + AI Summit は、Databricks が年に一度開催する世界最大級のデータ・AI のカンファレンスです。毎年、サンフランシスコの Moscone Center で開催されています。今年は6月15日から18日まででした。

https://www.databricks.com/dataaisummit

実は前回も参加していたのですが、今回は規模が明らかに大きくなっていました。公式発表によると、現地参加は174カ国から31,309名。日本からも500名くらいは来ていたと聞きました。そして何より、今回も発表される機能がとても多かったです。

## Summit と Keynote の全体感：4つの C

Databricks CEO の Ali Ghodsi は、Keynote の冒頭で会場に「AGI はもう来ていると思う人？」と問いかけました。3万人のうち約9割が「来ていない」に手を挙げた。それに対して彼は「あなたたちは間違っている。もう来ている」と言い切りました。

その上で繰り返していたのが「**AI に足りないのは知能ではなく、コンテキストだ（AI doesn't have an intelligence problem, it has a context problem）**」というメッセージです。モデルはもう十分賢い。にもかかわらず AI が業務で使われないのは、それを取り巻く課題が解けていないからだ、と。Day 2 のアジェンダはこの「4つの C」で構成され、午前の発表はそれぞれが C のどれかを製品にしたものでした。

<!-- ここに画像を入れる: 4つのC（Context / Cost / Control / Choice）の整理スライド -->

この整理を聞いて、自分のなかでも腑に落ちました。私はもともと、Databricks はもう単なるデータ基盤ではなく、AI が業務を理解して意思決定するための基盤に向かっていると考えていました。テーブルを渡せば AI は SQL を書ける。でも「売上に返品を含むのか」「どの顧客 ID と契約 ID を紐づけるのか」といった業務文脈がないと、AI はもっともらしいがズレた答えを出す。4つの C は、まさにこのズレを埋めるための課題設定で、自分の感覚と同じ方向を向いていました。

そしてもう一つ、全体を貫いていたのが「**Lakehouse はエージェント時代の OS**」という位置づけです。これまで「データウェアハウスの代替」だった Databricks が、今回は OLTP（Lakebase）、リアルタイム分析（Lakehouse//RT）、ストリーミング取り込み（ZeroBus）、エージェント開発（Agent Bricks）、アプリ実行基盤（Apps）、マーケティング（CustomerLake）まで、すべてを Unity Catalog のガバナンス配下に統合してきました。「Databricks の中で完結させる」という設計を、本気で進めている印象でした。

## Keynote で発表された新機能

今回発表された機能を、まず一覧でまとめておきます。数が多いので、4つの C を軸にざっと並べました。

| カテゴリ | 主な発表 | ひとことで |
|---|---|---|
| Context | Genie One / Genie Ontology / Genie Code / Genie ZeroOps | 自然言語でデータを扱う AI 同僚と、その土台になる文脈レイヤー |
| Context | Unity Catalog Metrics / Catalog Federation | 指標定義の統一と、カタログ横断のアクセス |
| Cost | Unity AI Gateway | LLM のコスト・ルーティング・トレースを一元管理 |
| Control | Agent Bricks | エージェントを安全に作って運用する基盤 |
| Control | Omniagent（OSS） | エージェントハーネスを統一インターフェースで切り替える OSS |
| Control | Lakewatch（Panther 買収） | セキュリティ運用をエージェントのワークロードに |
| Choice / データ基盤 | LTAP / Lakebase | OLTP と OLAP を単一データで統合する新アーキテクチャ |
| Choice / データ基盤 | Lakehouse//RT | Reyden エンジンによるリアルタイム分析（Beta） |
| Choice / データ基盤 | ZeroBus | メッセージバス不要のサーバーレス取り込み API |
| データ基盤 | Lakeflow Designer | エージェント時代のデータエンジニアリング |
| アプリ | CustomerLake | Lakehouse に組み込まれたエージェント型 CDP |
| ML | AI Runtime / Feature Store 拡張 / Model Serving 拡張 | サーバーレス GPU と、ML 基盤の各コンポーネント強化 |

ここから、特に気になったものをいくつか取り上げます。

### Context：Genie One / Genie Ontology

https://www.databricks.com/jp/blog/introducing-genie-one-genie-ontology-and-genie-agents

Genie One は、Slack や Teams、モバイルアプリから自然言語でデータに問い合わせられる「AI の同僚」です。その裏側で効いているのが Genie Ontology で、テーブルやクエリ、ダッシュボードから組織の概念や指標、関係性を自動で抽出してグラフにします。OntRank という、Google の PageRank の発想を借りた仕組みで「どのデータソースが信頼できるか」まで判定してくれるのがおもしろい。

自分が見ているのはまさにこの Context の部分です。テーブルを渡すだけでは AI は正しく答えられない。意味を渡す層を製品として持ってきたのは、素直にうらやましいと思いました。

<!-- ここに画像を入れる: Genie One / Genie Ontology の画面 or 概念図 -->

### Cost：Unity AI Gateway

https://www.databricks.com/blog/introducing-ai-spend-controls-unity-ai-gateway

LLM のコスト・ルーティング・トレーシングを一箇所に集めるレイヤーです。ユーザーやワークスペース、アカウント単位で予算アラートを出したり、上限に達したらリクエストを自動で止めるハードキャップを設定できます。

Ghodsi が Keynote で「これはものすごく高くつくぞ」とはっきり言っていたのが印象的でした。AI を本番で叩く機会が増えてきた今、チーム単位の予算上限とトレースを早めに入れておくべきだと痛感しました。

<!-- ここに画像を入れる: Unity AI Gateway の予算管理画面 -->

### Control：Agent Bricks

https://www.databricks.com/jp/blog/agent-bricks-dais-2026

エージェントを安全に作って運用するための基盤です。Unity Catalog に MCP（Model Context Protocol）サポートが入り、Google Drive や JIRA、Slack、GitHub といった外部ソースへ安全につなげるようになりました。セキュリティポリシーや予算管理を組み込んだまま動かせます。

社内でエージェントを増やしていくなら、最初からこういう土台に乗せて作るほうが結局ラクだろうな、と思いました。

<!-- ここに画像を入れる: Agent Bricks の構成図 or 画面 -->

### Choice：LTAP / Lakebase

https://www.databricks.com/company/newsroom/press-releases/databricks-launches-ltap-first-lake-transactionalanalytical

LTAP（Lake Transactional/Analytical Processing）は、トランザクション処理（OLTP）と分析処理（OLAP）を、レイク上の単一のデータコピーで統合するアーキテクチャです。40年間ずっと別々のシステムに分かれていた処理を、ETL や CDC を挟まずに一つにする、という話でした。その OLTP 側を担うのが、Postgres 互換のサーバーレス DB、Lakebase です。

Git 風のブランチング機能で本番データに対して安全に実験できるようになるのは、開発者として地味にうれしいポイントでした。Iceberg 統合と合わせて、特定の製品やクラウドに縛られない選択肢（Choice）を取りやすくなる方向です。

<!-- ここに画像を入れる: LTAP のアーキテクチャ図 -->

### CustomerLake：いちばん気になった発表

https://www.databricks.com/blog/introducing-customerlake-agentic-cdp

データ基盤をやっている立場として、いちばん気になったのが CustomerLake です。Databricks がマーケティング領域に進出して出してきた、エージェント型の CDP（顧客データ基盤）でした。Profile Agent が生の顧客データを Customer 360 プロファイルに整え、Campaign Agent が顧客のシグナルから「次の最適なアクション」を決めてチャネルをまたいで実行します。

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
