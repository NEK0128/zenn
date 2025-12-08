---
title: "Delta Sharingで実現するセキュアな企業間データ連携 —— 導入から検証までの実践ガイド"
emoji: "🔗"
type: "tech"
topics: ["Databricks", "DeltaSharing", "データ連携"]
published: false
publication_name: "ivry"
---

# はじめに

本記事は、Databricks Adevent Calendar 2025 10日目の記事です。
<https://adventar.org/calendars/11552>

こんにちは、IVRy でデータエンジニアとして働いている松田 健司([@ken_3ba](https://x.com/ken_3ba))と申します。趣味はビリヤードでプロの試合にも出ていたりするぐらい割とガチでやっています。先日も尼崎でプロの一番大きな大会に出場しました！1勝もできませんでしたが笑

![billiard](/images/IMG_2790.jpg)

本記事はビリヤードの記事とは全く関係なく、Databricksを活用した企業間データ連携についてお話ししますのでご安心ください！

今の時代、AIを最大限に活用するためにデータは必要不可欠で、さらに、自社だけでなく他企業とデータ連携することでより大きな価値を生み出すことができます。

例えばIVRyでは、お客様のデータを取り込むことでAIによるパーソナライズした提案が可能になり、また、IVRyのプロダクトで得られたデータをお客様に返却し、独自の分析に活用いただくこともできます。

しかし、以下のような問題があり、データ連携を実現することは簡単ではありません。

- セキュリティリスク
- ETLパイプラインの構築・運用コスト
- アクセス制御と監査の複雑さ

これらの課題を解決するのが、Databricksの**Delta Sharing**です。本記事では、Delta Sharingの概要と実際の検証結果をお伝えします。

# Delta Sharingとは

Delta Sharingは、Databricksが開発した**オープンなデータ共有プロトコル**です。企業間でのセキュアかつ効率的なデータ共有を実現します。

![Delta Sharing](/images/delta-sharing-65c7e412250042e93f75e44d077848aa.png)

<https://docs.databricks.com/aws/ja/delta-sharing/>

## Delta Sharingの特徴

| 特徴 | 説明 |
|------|------|
| **コピー不要のライブデータ共有** | 既存のDelta LakeやParquetデータをリアルタイムで共有。データの複製が不要 |
| **幅広いクライアント対応** | pandas、Tableau、Apache Spark、Power BIなど既存ツールから直接アクセス可能 |
| **セキュリティ・監査・ガバナンス** | 単一のアクセスポイントで許可、追跡、監査が可能 |
| **大容量データセット対応** | テラバイト規模のデータを経済的に共有 |

## 共有方式

Delta Sharingには主に2つの共有方式があります。

1. **Databricks-to-Databricks共有**: Unity Catalog対応ワークスペース間でのデータ共有（トークン不要）
2. **オープン共有プロトコル**: Databricks以外のあらゆるコンピューティングプラットフォームとの共有（Bearer tokenまたはOIDC使用）

### 共有可能なアセット

- Delta テーブル、ビュー、ストリーミングテーブル、マテリアライズドビュー
- Unity Catalog ボリューム
- 機械学習モデル
- Databricksノートブック（Databricks-to-Databricks共有のみ）

# Delta Sharingを検証してみた

今回は**Databricks-to-Databricks共有**を検証しました。

## サマリー

### データ連携の即時性

**ほぼリアルタイム（2〜3分程度）**でデータ連携が可能です。
共有できるオブジェクトは以下の通りです。

- テーブル（Delta形式）
- ビュー
- マテリアライズドビュー
- ボリューム（Databricks間のみ）
- ノートブック（Databricks間のみ）
- 機械学習モデル（Databricks間のみ）

### セキュリティとガバナンス

- データアセットの追加や受信者の設定には**メタストア管理権限が必要**
- 利用状況は**監査ログで追跡可能**
- 受信者は**Read Only権限のみ**で、データの追加・編集・削除は不可

### 簡易性

後述の手順の通り、GUIで簡単に連携を設定できます。また、[Terraform](https://registry.terraform.io/providers/databricks/databricks/latest/docs/resources/share)での管理も可能なため、ガバナンスを効かせた運用もできます。

利用者は通常のテーブルと同様にSQLでクエリできます。

### コスト

Delta Sharing自体の追加コストは発生しません。**利用時のコンピュートリソースのみ**が受信者側に課金されます。

## セットアップ手順

### Step 1: [提供側]アカウントコンソールでDelta Sharingを有効化

アカウントコンソールにログインし、組織外の関係者とのDelta Sharingを有効化します。
有効期限は無制限に設定することも可能ですが、今回は90日で設定します。

![Delta Sharing enable](/images/ds_1.png)

### Step 2: [提供側&受信側]メタストア管理者を設定する

Delta Sharingを利用するには、メタストア管理者の設定が必要です。実際に共有設定を行うユーザーが所属するグループに権限を追加してください。

![Delta Sharing metastore](/images/ds_2.png)

#### Step 3: [受信側]共有識別子を取得

受信者側でカタログの設定から「Delta Sharing」を選択し、共有識別子をコピーして提供側に共有します。

![Delta Sharing id_1](/images/ds_3.png)
![Delta Sharing id_2](/images/ds_4.png)

#### Step 4: [提供側]受信者を登録

受信者から受け取った共有識別子を使用して、新規受信者を作成します。

![Delta Sharing register recipient 1](/images/ds_10.png)
![Delta Sharing register recipient 2](/images/ds_5.png)

以下のように一覧に表示されれば、作成は完了です。
![Delta Sharing register recipient 3](/images/ds_6.png)

#### Step 5: [提供側]共有するデータを選択

カタログから該当のテーブル（またはスキーマ）を選択し、「Delta Sharing経由で共有」を実行します。

![Delta Sharing register data 1](/images/ds_7.png)

共有名を任意で設定し、受信者を先ほど作成したものに指定します。

![Delta Sharing register data 2](/images/ds_8.png)

指定すると、以下のように連携したアセットが表示され、受信側で利用できるようになります。

![Delta Sharing register data 3](/images/ds_9.png)

#### Step 6: [受信者側]カタログを作成

「自分と共有」の一覧から共有されたデータを確認し、カタログを作成してアクセス権限を設定します。

提供者側でデータ連携が完了すると以下のように一覧に表示され、連携対象をクリックします。
![Delta Sharing create catalog 1](/images/ds_11.png)

そして、カタログを作成し、権限やメタ情報などの設定をするとデータ連携され、通常と同様にSQLでクエリすることが可能になります。
![Delta Sharing create catalog 2](/images/ds_12.png)
![Delta Sharing create catalog 3](/images/ds_13.png)

## 補足

### 監査ログの活用

Delta Sharingでは、`system.access.audit`テーブルで詳細な監査ログを取得できます。

**提供側で確認できる情報**:

- 受信者のクエリ利用状況
- データアセットの追加・権限更新のログ
- エラーログ

これらのログを活用することで、セキュリティアラートの設定や利用状況の可視化が可能です。

### 制約事項

Delta Sharingには以下の制約があります。

| 制約 | 詳細 |
|------|------|
| スナップショットのみ | CDFやストリーミングの連携は不可 |
| パーティションフィルタリング | リキッドクラスタリングテーブルでは使用不可 |
| カタログ単位の共有 | カタログ全体の共有は不可（スキーマ単位は可能） |
| 音声データ | Databricks間でのみ共有可能 |

## まとめ

Delta Sharingは、企業間のセキュアなデータ連携を実現する強力なソリューションです。

- **簡易なセットアップ**: GUIで直感的に設定可能
- **ニアリアルタイム連携**: 2〜3分程度でデータ同期
- **セキュアなアクセス制御**: メタストア管理者による一元管理
- **追加コスト不要**: コンピュートリソースのみの課金
- **充実した監査機能**: 利用状況の追跡と可視化

お客様へのデータ提供、パートナー企業との共同利用、グループ会社間でのデータ連携といったユースケースに適しています。

今回はDatabricks間の連携のみ検証したため、異なるプラットフォームとの連携については別途調査が必要です。

企業間データ連携を検討されている方は、Delta Sharingを選択肢の一つとしてご検討してみてはいかがでしょうか。この記事が役に立てれば幸いです。
