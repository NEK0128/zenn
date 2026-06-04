---
title: "Databricks AppsのUser authorization（U2M）を試す —— ユーザーごとにカタログを出し分ける"
emoji: "🔐"
type: "tech"
topics: ["Databricks", "UnityCatalog", "認証", "OAuth", "データ基盤"]
published: false
publication_name: "ivry"
---

# はじめに

こんにちは、IVRy でデータエンジニアとして働いている松田 健司([@ken_3ba](https://x.com/ken_3ba))と申します。趣味はビリヤードで、プロの試合にも出ていたりするぐらい割とガチでやっています。

余談ですが、最近のビリヤード漫画だと岡Q先生の『ミドリノバショ』（小学館）が面白かったです。作中には手前の球を飛び越える **ジャンプショット** も出てきます。漫画では足で固定してジャンプショットをしていますが（たぶん反則な気がする）、足で固定しない普通のジャンプショット自体は実際にあり、自分もよく使います。

![『ミドリノバショ』作中のジャンプショットの一コマ。台に脚を乗せて撃っている](/images/databricks-apps-user-authorization/midorinobasho-jump-shot.jpeg)
*岡Q『ミドリノバショ』（小学館）より*

ビリヤードの話はこの辺にして、本記事は Databricks Apps の認可機能「U2M」を試してみた話です。

# 背景・課題：ユーザーごとにカタログを出し分けたい

Databricks Apps でアプリを構築するとき、こんな要件に出くわします。

- ユーザー A はカタログ α を閲覧できる
- ユーザー B はカタログ α を閲覧できない

社内向けデータアプリを作ると、「部署ごと・チームごとに見せるデータを分けたい」という要望はよく発生します。ところが、Databricks Apps をデフォルト設定のまま作ると、そのアプリにアクセスできる人は全員が同じカタログを閲覧できてしまいます。これは認可モデルによるものです。

# デフォルトの認可モデル（App authorization）について

なぜユーザーごとに分けられないのか。Databricks Apps はデフォルトで **App authorization** という認可モデルで動きます。

これは、アプリ専用の **サービスプリンシパル（SP）** でデータにアクセスするモデルです。SP とは、人間ではなくアプリやジョブが使う「機械用のアカウント」のことです。

App authorization では、誰がアプリにログインしても、データアクセスは常にこの SP 1 つの権限で行われます。つまり、SP がカタログ α を見られる設定なら、**ログインした全員がカタログ α を見られてしまう**わけです。アプリにログインしたユーザーが誰であっても、データアクセスの権限は同一になり、ユーザーごとに出し分けすることができません。

# User authorization（U2M）の仕組み

このユーザー別制御を実現するのが **User authorization** です。U2M（User-to-Machine）認証とも呼ばれ、「人間のユーザー本人の認証情報で機械（アプリ）がデータにアクセスする」モデルです。

最近 Public Preview としてリリースされた機能で、まさに今回やりたかった「ユーザーごとにアクセスできるデータを分ける」を実現できます。

仕組みは次の通りです。

1. ユーザーがアプリにアクセスすると、Databricks がログインユーザーのアクセストークンをリクエストヘッダ `x-forwarded-access-token` でアプリに渡す
2. アプリ側はそのトークンを使って SQL Warehouse 等に接続する
3. Unity Catalog のポリシー（カタログ一覧、テーブルクエリ、行レベルフィルタ、カラムマスク）が**ユーザー単位**で適用される

ポイントは、アプリが「アプリ自身の権限」ではなく「ログインユーザー本人の権限」でデータにアクセスする点です。Unity Catalog で組んだ権限設計がそのままアプリの見え方に反映されます。

> 出典: [Databricks Apps での認可（Databricks 公式ドキュメント）](https://docs.databricks.com/aws/ja/dev-tools/databricks-apps/auth)

# 実際にためしてみた

ログインユーザーの権限で Unity Catalog のテーブルを SELECT するだけの、最小の Streamlit アプリで試してみました。

## アプリ本体（app.py）

肝になるのは `x-forwarded-access-token` ヘッダーを読み、その値をそのまま `databricks-sql-connector` の `access_token` に渡すところです。

```python
import os

import pandas as pd
import streamlit as st
from databricks import sql

st.title("Databricks Apps × U2M デモ")

# Databricks Apps は、ログインしたユーザー本人の OAuth アクセストークンを
# X-Forwarded-Access-Token ヘッダーに載せて後段アプリに渡してくれる。
user_access_token = st.context.headers.get("x-forwarded-access-token")
user_email = st.context.headers.get("x-forwarded-email", "(unknown)")
st.write(f"**ログインユーザー**: `{user_email}`")

if not user_access_token:
    st.error(
        "x-forwarded-access-token ヘッダーが取得できません。"
        "User authorization が有効か、scope に sql が含まれているか確認してください。"
    )
    st.stop()

server_hostname = os.environ["DATABRICKS_HOST"].replace("https://", "").rstrip("/")
http_path = os.environ["DATABRICKS_WAREHOUSE_HTTP_PATH"]


@st.cache_data(ttl=60)
def fetch_table(token: str) -> pd.DataFrame:
    # U2M の肝はこの access_token=token の1行。
    # アプリの SP ではなくログインユーザー本人のトークンを渡すことで、
    # Unity Catalog の GRANT や行レベルセキュリティがそのまま効く。
    with sql.connect(
        server_hostname=server_hostname,
        http_path=http_path,
        access_token=token,
    ) as conn, conn.cursor() as cursor:
        cursor.execute("SELECT * FROM your_catalog.your_schema.your_table")
        return cursor.fetchall_arrow().to_pandas()


df = fetch_table(user_access_token)
st.dataframe(df, use_container_width=True)
```

App authorization との違いは `access_token` に何を渡すかだけです。アプリの SP のトークンではなく、ヘッダーから取り出したユーザー本人のトークンを渡します。これでデータアクセスの主体がアプリからログインユーザー本人に変わります。

## 必要な設定：アプリ側で `sql` スコープを付ける

U2M を使うには、アプリ側で `sql` スコープを付ける設定が必要です。スコープを付けておくと、初回アクセス時に OAuth の同意画面が表示されます。

![U2M の OAuth 同意画面。アプリが要求するスコープが一覧表示される](/images/databricks-apps-user-authorization/u2m-consent-screen.png)
*初回アクセス時に表示される OAuth の同意画面*

スコープはアプリ作成時に付与します。

```bash
databricks apps create u2m-demo
databricks apps update u2m-demo --json '{"user_api_scopes":["sql"]}'
```

付け忘れるとトークンは発行されるものの SQL Warehouse 接続時に **403 Forbidden** で弾かれるので、設定漏れに注意してください。

## 実行結果

このアプリをデプロイして開いた画面が次のものです。

![U2M デモアプリの実行結果。ログインユーザーの権限で SELECT した結果が表示されている](/images/databricks-apps-user-authorization/u2m-app-result.png)
*ログインユーザー本人の権限でクエリが実行され、結果が表示される（テーブル名・アカウント情報はマスクしています）*

権限のあるユーザーで開くと結果が表示され、Unity Catalog で権限を与えていない別ユーザーで開くと SELECT が失敗します。アプリ側でフィルタのロジックを書くことなく、Unity Catalog の権限設計だけで出し分けが実現できました。

# 注意事項

- User authorization は 2026 年 6 月時点で **Public Preview** です。本番利用の前に GA の状況を確認してください。
- 仕様（ヘッダ名・スコープ・有効化手順）は変わる可能性があります。実装時は公式ドキュメントの最新版を確認してください。
- 利用にはワークスペース管理者による事前の有効化が必要です。

# まとめ

Databricks Apps でユーザーごとのアクセス制御を実現したいなら、**User authorization（U2M）** がおすすめです。

- App authorization（デフォルト）は SP の権限を全員で共有する。1つの権限で全員に同じデータを見せるアプリには十分
- ユーザーごとに見せるデータを分けたいなら User authorization。ログインユーザー本人の権限で Unity Catalog のポリシーがそのまま適用される
- アプリのコードでフィルタを書く必要がなく、Unity Catalog の権限設計だけでユーザーごとの出し分けが実現できる

アプリ側でフィルタを実装するより安全で、運用も楽になります。社内向けデータアプリでユーザーごとのアクセス制御に悩んでいる方は試してみてください。

---

IVRyではキャリア登録やカジュアル面談の機会をご用意しています。ご興味のある方はぜひ以下よりお申し込みください。

https://herp.careers/v1/ivry/wmZiOSAmZ4SQ