---
title: "Data + AI Summit 2026 現地参加レポート：ビリヤードボールと4つのCを持ち帰った"
emoji: "🎱"
type: "idea"
topics: ["Databricks", "DataAISummit", "AI", "データエンジニアリング", "IVRy"]
published: false
publication_name: "ivry"
---

## まずはビリヤードボールの話から

Data + AI Summit（以下 Summit）に参加してきました。新機能の話やキーノートの話もしますが、最初に一番大事な話をさせてください。ビリヤードボールです。

Summit には会場のあちこちにクエストが用意されています。セッションを聞いたり、ブースを回ってスタンプを集めたりして達成すると、Databricks のロゴが入ったビリヤードボールがもらえる。これがなぜかコレクター心をくすぐるんです。色違いを揃えたくなる。今年も真面目にクエストをこなして、ちゃんと集めてきました。

https://x.com/Data_AI_Summit/status/1933245536389198198

https://x.com/ken_3ba/status/2067036954949239180

<!-- ここに画像を入れる: もらってきたビリヤードボールの写真 -->
*クエストを達成するともらえる Databricks のビリヤードボール*

そして、ここからが心残りの話です。会場のすぐ近くに、本物のビリヤード場もあったらしいのです。ボールはもらったのに、肝心のビリヤードは打ってこなかった。これが今回いちばんの後悔です。次に行くときは、セッションより先にまずビリヤード場の予約を取ります。

https://x.com/myshmeh/status/2068471465746243624

## TL;DR

- Databricks の通底メッセージは「**AGI はもう来ている。問題は知能ではなく、その周りの4つの C（Context / Cost / Control / Choice）だ**」。Lakehouse を「エージェント時代の OS」と位置づけ、OLTP からマーケまで Unity Catalog 配下に垂直統合してきた。
- 注目の新機能は **Genie One / Ontology（Context）、Unity AI Gateway（Cost）、Agent Bricks（Control）、LTAP・Lakebase（Choice）、CustomerLake**。Databricks は「AI がデータを理解し意思決定する基盤」に振り切っていた。
- そして何より、現地に行く価値が大きい。発表をオンラインで見るのと、現場の熱狂の中で聞くのとでは、記憶への残り方も「帰ったら試すぞ」というモチベーションも全然違う。

## IVRy は Databricks を導入したばかり

本題に入る前に立場を明かしておきます。私は IVRy のデータ基盤チームのデータエンジニアです。

IVRy は2025年7月、データ・AI 活用を加速させる新基盤として、Databricks を中心としたプラットフォームを構築しました。まだ導入したばかりです。だからこそ、Databricks がこの先どこへ向かうのかを自分の目で確かめたくて、今回の Summit に参加しました。

ちなみに、通話やメールなどのコミュニケーションデータを AI が扱える形に変える「IVRy Data Hub」というプロダクトも、Databricks 基盤の上で動いています。この記事でも何度か出てきます。

https://ivry.jp/function/datahub/

導入の背景や採用理由は、同僚の大曽根が登壇した資料が詳しいので、そちらをどうぞ。

https://speakerdeck.com/keisukeosone/gendatoivry-ji-cheng-chang-sutatoatupugadatabrickswocai-yong-sitali-you-data-plus-ai-world-tour-2025

## Databricks Data + AI Summit とは

Data + AI Summit は、Databricks が年に一度開催する世界最大級のデータ・AI のカンファレンスです。今年はサンフランシスコの Moscone Center で6月15日から18日まで開催されました。

https://www.databricks.com/dataaisummit

実は前回も参加していたのですが、今回は規模が明らかに大きくなっていました。公式発表によると、現地参加は **174カ国から31,309名**。これにバーチャル参加が加わり、ブレイクアウトセッションは800以上、OpenAI と組んだ複数日のハッカソンまでありました（[公式の開催概要](https://www.databricks.com/company/newsroom/press-releases/databricks-announces-2026-data-ai-summit-keynote-lineup-and)）。日本からの参加者も多く、昨年よりはっきり増えた実感があります。会場の熱量も、発表される機能の数も、一年でここまで変わるのかと驚かされました。

## Summit と Keynote の全体感：4つの C

ここからが、今回いちばん伝えたい部分です。

Databricks CEO の Ali Ghodsi は、Keynote の冒頭で会場に「AGI はもう来ていると思う人？」と問いかけました。3万人のうち約9割が「来ていない」に手を挙げた。それに対して彼は「あなたたちは間違っている。もう来ている」と言い切りました。

その上で繰り返していたのが「**AI に足りないのは知能ではなく、コンテキストだ（AI doesn't have an intelligence problem, it has a context problem）**」というメッセージです。モデルはもう十分賢い。にもかかわらず AI が業務で使われないのは、それを取り巻く課題が解けていないからだ、と。Day 2 のアジェンダはこの「4つの C」で構成され、午前の発表はそれぞれが C のどれかを製品にしたものでした（[BigDATAwire の Keynote レポート](https://hpcwire.com/bigdatawire/2026/06/16/ali-ghodsis-keynote-ai-doesnt-have-an-intelligence-problem-it-has-a-context-problem/)）。

| C | 課題 | 対応する主な発表 |
|---|---|---|
| **Context** | 組織のデータや業務プロセスを、AI に正しく理解させたい | Genie Ontology、OntRank |
| **Cost** | エージェントの利用が増えると AI コストが青天井になる | Unity AI Gateway（予算アラート・ハードキャップ） |
| **Control** | エージェントが本番 DB を壊す、PII が漏れるリスクを抑えたい | Agent Bricks、Databricks Sandbox |
| **Choice** | 特定モデル・特定クラウドへのロックインを避けたい | LTAP、Lakebase、Iceberg 統合 |

この整理を聞いて、自分のなかでも腑に落ちました。私自身、Databricks は今回 **AI がデータを理解して意思決定するための基盤**へ舵を切ったと思っています。テーブルを渡せば AI は SQL を書ける。でも「売上に返品を含むのか」「どの顧客 ID と契約 ID を紐づけるのか」といった業務文脈がないと、AI はもっともらしいがズレた答えを出す。4つの C は、まさにこのズレを埋めるための課題設定でした。

そしてもう一つ、全体を貫いていたのが「**Lakehouse はエージェント時代の OS**」という位置づけです。これまで「データウェアハウスの代替」だった Databricks が、今回は OLTP（Lakebase）、リアルタイム分析（Lakehouse//RT）、ストリーミング取り込み（ZeroBus）、エージェント開発（Agent Bricks）、アプリ実行基盤（Apps）、マーケティング（CustomerLake）まで、すべてを Unity Catalog のガバナンス配下に統合してきました。「Databricks の中で完結させる」という設計を、本気で進めている印象でした。

https://x.com/minicoohei/status/2062252685701841152

## Keynote で注目した新機能

発表された機能は数十にのぼります。全部は追えないので、4つの C に沿って、特に気になったものを「どんな機能か」と「自分はこう使えそうだと思った」の2点に絞って紹介します。

### Context：Genie One / Genie Ontology

https://www.databricks.com/jp/blog/introducing-genie-one-genie-ontology-and-genie-agents

Genie One は、Slack や Teams、モバイルアプリから自然言語でデータに問い合わせられる「AI の同僚」です。その裏側で効いているのが Genie Ontology で、テーブルやクエリ、ダッシュボードから組織の概念や指標、関係性を自動で抽出してグラフにします。

おもしろいのが OntRank という仕組みです。Google の PageRank の発想を借りて、「誰が作ったか」「どれだけ使われているか」「いつ更新されたか」といった信号から、どのデータソースが権威あるソースかを判定する。同じ「売上」でも、どの定義を信じるべきかを選んでくれるわけです。Databricks の社内ベンチマーク（28問）では、一発目で 84.5% に正答したと発表されていました。

自分が見ているのはまさにこの Context の部分です。テーブルを渡すだけでは AI は正しく答えられない。意味を渡す層を製品として持ってきたのは、素直にうらやましいと思いました。

<!-- ここに画像を入れる: Genie Ontology / OntRank の概念図（Keynoteスライド or 公式ブログの図） -->

### Cost：Unity AI Gateway

https://www.databricks.com/blog/introducing-ai-spend-controls-unity-ai-gateway

LLM のコスト・ルーティング・トレーシングを一箇所に集めるレイヤーです。今回の目玉は予算管理で、ユーザー・ワークスペース・アカウント単位で予算アラートを出したり、上限に達したらリクエストを自動で止めるハードキャップを設定できるようになりました。

Ghodsi が Keynote で「これはものすごく高くつくぞ（This is going to get extremely expensive）」とはっきり言っていたのが印象的でした。エージェントが大量にクエリを投げる時代になると、消費は青天井になる。Databricks 自身、AI エージェントの増加でマージンが圧迫されているという報道もありました（[CNBC](https://www.cnbc.com/2026/06/16/databricks-revenue-growth-tops-80percent-to-6point9-billion-annualized.html)）。

これは自分たちにとっても他人事ではないです。AI を本番で叩く機会が増えてきた今、チーム単位の予算上限とトレースを早めに入れておくべきだと、現地で痛感しました。

<!-- ここに画像を入れる: Unity AI Gateway の予算アラート / ハードキャップ画面 -->

### Control：Agent Bricks

https://www.databricks.com/jp/blog/agent-bricks-dais-2026

エージェントを安全に作って運用するための基盤です。Unity Catalog に MCP（Model Context Protocol）サポートが入り、Google Drive や JIRA、Slack、GitHub といった外部ソースへ安全につなげるようになりました。セキュリティポリシーや予算管理を組み込んだまま動かせます。

「とりあえず作ってみた AI」を本番で安全に動かすところまで面倒を見てくれる。社内でエージェントを増やしていくなら、最初からこういう土台に乗せて作るほうが結局ラクだろうな、と思いました。

<!-- ここに画像を入れる: Agent Bricks の構成図 or 管理画面 -->

### Choice：LTAP / Lakebase

https://www.databricks.com/company/newsroom/press-releases/databricks-launches-ltap-first-lake-transactionalanalytical

LTAP（Lake Transactional/Analytical Processing）は、トランザクション処理（OLTP）と分析処理（OLAP）を、レイク上の単一のデータコピーで統合するアーキテクチャです。40年間ずっと別々のシステムに分かれていた処理を、ETL や CDC を挟まずに一つにする、という話でした。その OLTP 側を担うのが、Postgres 互換のサーバーレス DB、Lakebase です。

Lakebase はすでに多くの企業が使っていて、1日に1,200万回データベースが起動しているそうです。一方で LTAP 自体はこれから（coming soon）の段階。クロスクラウドの DR や Git 風のブランチング機能も発表されていて、本番データに対して安全に実験できるようになるのは、開発者として地味にうれしいポイントでした。Iceberg 統合と合わせて、特定の製品やクラウドに縛られない選択肢（Choice）を取りやすくなる方向です。

<!-- ここに画像を入れる: LTAP のアーキテクチャ図（OLTP + OLAP を単一ストレージに統合） -->

### CustomerLake：いちばん気になった発表

https://www.databricks.com/blog/introducing-customerlake-agentic-cdp

最後に、データ基盤をやっている立場としていちばん気になったのが CustomerLake です。Databricks がマーケティング領域に進出して出してきた、エージェント型の CDP（顧客データ基盤）でした。

注目は2つのエージェントです。Profile Agent が生の顧客データを Customer 360 プロファイルに整え、Campaign Agent が顧客のシグナルから「次の最適なアクション」を決めてチャネルをまたいで実行する。一回きりのキャンペーンではなく、文脈にリアルタイムで反応し続ける「infinity campaigns」という考え方を打ち出していました。現時点では Private Preview で、HP や Circle K などが先行利用しているそうです。

正直に言うと、これを聞いたとき少しドキッとしました。「コミュニケーションデータから、業務で使われる次のアクションを作る」という方向は、自分たちが目指しているものと重なる部分があるからです。Databricks 公式が同じ問題に乗り出してきたのは、市場があることの証明でもあるし、同時に「お前たちはどこで勝つのか」を問われている気もしました。考え込みながら会場を出たのを覚えています。

<!-- ここに画像を入れる: CustomerLake の Profile Agent / Campaign Agent の図 -->

ここで挙げた以外にも、ZeroBus（メッセージバスを使わないストリーミング）や Omniagent（OSS のエージェントハーネス）など、書ききれない発表がたくさんありました。全体像は Databricks Japan の振り返りや、丁寧にまとめてくださっている方々の記事が参考になります。

https://qiita.com/taka_yayoi/items/dd67ae8676d5df07e43d

https://x.com/DatabricksJP/status/2067810273701048459

## 実際に現地へ行ってみた感想

ここからは技術の話をいったん離れて、現地で感じたことを書きます。

まず、街ぐるみで Summit を知っていたことに驚きました。入国審査でも、空港からのタクシーでも、「Databricks のイベントに行くんだね」と言われる。サンフランシスコの街全体がこのカンファレンスを知っている空気でした。

会場は Keynote の開演前から長蛇の列。人気セッションの予約はほぼ全部埋まっていて、立ち見も出ていました。日本人同士の交流会も開かれて、いろいろな人とつながれた。そして最後は ORACLE PARK を貸し切ってのライブです。The Chainsmokers まで来ていて、「カンファレンスの打ち上げってこんな規模でやるの？」と完全にビビりました。

あと、これは技術と何の関係もないのですが、アメリカはとにかくなんでもデカい。ステーキを頼んだら、皿からはみ出る量が出てきました。残すのが申し訳なくて頑張って食べたら、翌朝のセッションで眠くなりました。

<!-- ここに画像を入れる: ORACLE PARK のクロージングライブの写真 -->
*クロージングは ORACLE PARK で。The Chainsmokers のライブまであった*

<!-- ここに画像を入れる: アメリカのデカいステーキの写真 -->
*アメリカのステーキ。量で圧倒してくる*

### 現地で聞くと、記憶への残り方が違う

Keynote はオンラインでも見られます。それでも、現地で生の発表を聞いてワクワクして、その場の熱狂に飲まれてテンションが上がる、という体験は大きい。リアルな現場でワクワクした方が記憶に残りやすいし、「帰ったら絶対に試すぞ」というモチベーションが湧いてくる。このライブ感は、配信では得られないものでした。

### 現地でしか得られない情報がある

セッションやブースで得られる情報は、現地でしか手に入りません。今回、私は IVRy の Data Hub に近いデータ基盤が、海外の事例ではどう動いているのかが気になっていて、それを中心に聞いて回りました。「こうやって Databricks を取り入れている」「ここで困っている」といった話を現地の人と直接交わせたのは、とてもいい持ち帰りになりました。

### 日本人同士のつながりが強くなった

海外に行くと、日本人とはとても話しやすい。同じ Databricks の発表を聞いて、同じ熱狂を共有できる。それだけで一気に親密になれました。同じイベントに参加して、同じものに興味を持っている人同士ならではの距離の縮まり方です。ちょうどワールドカップをやっていたのも、盛り上がった理由かもしれません。

### 行かせてもらえたことへの感謝

自分にとっては念願の初めての海外出張でした。とても刺激的で、学びも多くて、日本に戻って必ず還元したいと思いながら過ごしていました。

そして何より、渡航費が決して安くないなかで、快く送り出してくれた会社と、同じチームのメンバーには感謝しかありません。ぜひもう一度行きたいし、会社としても誰かは必ず参加した方がいい。そう断言できるイベントでした。

## まとめ

Data + AI Summit 2026 を一言でまとめると、「**Databricks が、データ基盤の会社から、AI がデータを理解し意思決定するための基盤の会社へ振り切った**」年でした。その整理が4つの C（Context / Cost / Control / Choice）であり、Genie Ontology や Unity AI Gateway、CustomerLake といった発表は、すべてこの C のどれかを埋めにいく機能でした。

IVRy は Databricks を導入したばかりです。今回見てきた方向性を、Data Hub にどう取り込むか、どこで自分たちの強みを出すか。持ち帰った宿題を一つずつ手元で試していきます。

そして、来年も誰かは現地に行くべきです。あのライブ感は、配信では味わえないので。
