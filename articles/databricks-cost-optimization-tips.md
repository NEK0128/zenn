---
title: "Databricksコスト最適化の実践ガイド —— データ取り込みのジョブコストを半分以上削減した"
emoji: "💰"
type: "tech"
topics: ["Databricks", "コスト削減", "AWS", "データエンジニアリング"]
published: false
publication_name: "ivry"
---

# はじめに

本記事は、Databricks Advent Calendar 2025 16日目の記事です。
https://qiita.com/advent-calendar/2025/databricks

こんにちは、IVRy でデータエンジニアとして働いている松田 健司([@ken_3ba](https://x.com/ken_3ba))と申します。趣味はビリヤードで、プロの試合にも出ているぐらい割とガチでやっています。最近ビリヤードのキューを買いました！見てくださいこの派手さ！一目惚れして、なんと40万円も奮発してしまいました！

![billiard](/images/databricks-cost-optimization-tips/IMG_2860.jpg)

ビリヤードの話は無限にできるのでここらへんで切り上げて、本題のDatabricksのコスト最適化についてお話しします。ビリヤードの小話はまた次回のブログをお楽しみに！

クラスターの概念に馴染みがない方は、本記事で紹介する設定を見直すことでコスト削減が見込めます。ぜひ参考にしてみてください。

# TL;DR

BigQueryからDatabricksへ移行後、デフォルト設定のままではコストが増加してしまいました。そこで以下の最適化を実施し、**DatabricksとAWSの料金を合わせて半分以上のコスト削減**を実現しました。

- **共通**: Photon機能の利用停止
- **クラシックコンピュート**: Workerへのスポットインスタンス活用、クラスターサイズ・タイプの見直し（r6g系への変更）、ワーカー数の最適化、Spark不使用ジョブのシングルノード化
- **サーバレス**: パフォーマンス最適化機能の無効化（performance_target: STANDARD）

# BQからDatabricksへの移行と課題

IVRyでは、データ分析基盤としてBigQueryを利用していましたが、以下の理由から2025年7月にDatabricksへの移行をしました。

- **転送コストの削減**: AWS〜Google Cloud間のデータ転送コストが大きな負担に
- **非構造化データのリアルタイム活用**: 通話文字起こしなどの非構造化データを即座に分析可能に
- **データガバナンスの強化**: データと権限を一元管理し、セキュリティとコンプライアンスを向上
- **LLM開発の生産性向上**: MLflowやモデルサービングなど、AI/ML開発に必要な機能が統合

現在のDatabricksアーキテクチャは以下の通りです。

![Databricksアーキテクチャ](/images/databricks-cost-optimization-tips/アーキテクチャ.png)

https://findy-tools.io/companies/ivry/90/76

しかし、**そのまま移行しただけではコストはむしろ増加**しました。

移行スケジュールが切羽詰まっていたため、まずは移行を優先し、デフォルト設定のままコンピュートを利用していました。その結果、必要以上のリソースを消費していることが判明し、コスト最適化に本格的に取り組むことになりました。

# Databricksのコスト構造

コスト最適化を行う前に、Databricksのコスト構造について軽くお話しします。

## Databricksとクラウドプロバイダーとのコスト関係

Databricksは各クラウドプロバイダー上に構築されます。Databricksがコントロールプレーンとして動作し、実際のコンピュート処理はクラウドプロバイダーのリソースを利用します。

IVRyではAWS上でDatabricksを利用しているため、**Databricksの利用料金**と**AWSのリソース料金（EC2、S3、ネットワーク等）の両方**が発生します。

![Databricks On Cloud Provider](/images/databricks-cost-optimization-tips/architecture.png)

https://docs.databricks.com/aws/en/getting-started/high-level-architecture

## DBU（Databricks Unit）について

Databricksの課金単位は**DBU（Databricks Unit）**です。DBUは処理能力を正規化した単位で、**利用するプロダクトによって1DBUあたりの料金が異なります**。

| プロダクト | 説明 |
|-----------|------|
| Jobs Compute | バッチ処理やETLジョブの実行 |
| SQL | データウェアハウスでのクエリ実行 |
| All-Purpose Compute | Notebookでのインタラクティブな分析 |
| Delta Live Tables | ストリーミング・バッチパイプラインの構築 |
| Model Serving | MLモデルのリアルタイム推論 |

例えば、同じインスタンスタイプでもJobs ComputeとAll-Purpose Computeでは1DBUあたりの単価が異なります。さらに最近では**Databricks Apps**など新しいプロダクトも続々とリリースされており、それぞれに応じた料金体系が設定されています。

正直、プロダクトごとに料金が異なるため結構複雑ですよね。。Jobs ComputeはAll-Purpose Computeよりも1DBUあたりの単価が安いため、バッチ処理にはJobs Computeを使用するなど、用途に応じた適切なプロダクト選定がコスト最適化の鍵となります。

https://www.databricks.com/jp/product/pricing

## サーバレスとクラシックコンピュート

Databricksには**サーバレス**と**クラシックコンピュート**の2つのコンピュートオプションがあります。

| 項目 | サーバレス | クラシックコンピュート |
|------|-----------|---------------------|
| インフラ管理 | Databricksが管理 | ユーザーが管理 |
| 課金 | DBUのみ | DBU + クラウドリソース料金 |
| 起動時間 | 高速 | クラスター起動が必要 |
| カスタマイズ性 | 制限あり | 柔軟に設定可能 |

クラシックコンピュートは利用しているクラウドのコンピュートリソースを直接使用します。IVRyの場合はAWSのEC2インスタンスを利用するため、**DatabricksのDBU料金**と**AWSのEC2料金**の両方が発生します。そのため、EC2も考慮したコスト最適化が必要です。

https://docs.databricks.com/aws/ja/compute/use-compute

# コスト最適化の具体的な施策

今回の最適化対象は**外部データソースからDatabricksへデータを取り込むジョブ**です。取り込みジョブにはクラシックコンピュートで動作するものとサーバレスで動作するものの両方があったため、それぞれに対応した最適化を行いました。

## 共通の対応

### Photon機能の利用停止

Photonは高速なクエリ実行を実現する機能ですが追加でDBU消費が発生します。処理速度よりもコストを重視するジョブでは、Photonを無効化することでコスト削減が可能です。

## クラシックコンピュート固有の対応

### スポットインスタンスの活用

Sparkは大規模データを複数のノードで分散処理し、効率的に処理する技術でIVRyでも幅広く利用しています。Sparkクラスターは、ジョブを管理するDriverノードと実際の処理を行うWorkerノードで構成されます。

![Sparkクラスター構成](/images/databricks-cost-optimization-tips/spark_cluster.png)

https://qiita.com/taka_yayoi/items/31190da754106b2d284e

Driverノードは中断されるとジョブ全体がエラーになりますが、Workerノードは中断されても他のWorkerで復旧可能です。この特性を活かし、Workerのみスポットインスタンスに切り替えました。

- **Driver**: オンデマンドインスタンスを維持
- **Worker**: スポットインスタンスで大幅なコスト削減

Databricks Asset Bundles（DAB）では、以下のように設定できます。

```yaml
resources:
  jobs:
    example_job:
      job_clusters:
        - job_cluster_key: "main_cluster"
          new_cluster:
            aws_attributes:
              first_on_demand: 1  # 最初の1台（Driver）はオンデマンド
              availability: SPOT_WITH_FALLBACK  # Workerはスポット優先
```

`first_on_demand: 1`でDriverをオンデマンドに、`availability: SPOT_WITH_FALLBACK`でWorkerをスポットインスタンス優先（取得できない場合はオンデマンドにフォールバック）に設定しています。

### クラスターサイズの最適化

メトリクスを確認しながら、必要以上に大きなインスタンスを使用していないか見直しました。CPU・メモリ使用率に余裕がある場合は、xlargeやlargeサイズへダウングレードすることでAWSのEC2コストを削減できます。

ただし、ダウングレードしすぎると「Could not reach driver」エラーが発生することがあり、Driverがメモリ不足で落ちている可能性が高いです。下記のようにメモリが逼迫している場合は、クラスターサイズをアップグレードする必要があります。

![クラスターメトリクス](/images/databricks-cost-optimization-tips/metrics.png)

### クラスタータイプの最適化

元々r6id系のクラスターを利用していましたが、メモリ最適化されコストが安いr6g系に変更しました。ただし、r6g系はインスタンスストレージを持たないため、EBSボリュームを追加する必要があります。

Databricks Asset Bundlesでは以下のように設定できます。

```yaml
resources:
  jobs:
    example_job:
      job_clusters:
        - job_cluster_key: "main_cluster"
          new_cluster:
            node_type_id: "r6g.large"
            aws_attributes:
              availability: SPOT_WITH_FALLBACK
              ebs_volume_type: GENERAL_PURPOSE_SSD
              ebs_volume_count: 1
              ebs_volume_size: 32
```

### ワーカー数の最適化

Sparkはワーカー数が増えるほど料金がかかるため、適切なコントロールが必要です。処理時間がかかっても良いがコストを抑えたい場合は、ワーカー数を最小限に設定することを推奨します。

ワーカー数の最適化は、Spark UIでExecutorがどれくらいアクティブになっているかを確認しながら行います。下記の例では2台のExecutorが動いているため、それに合わせて最適なワーカー数にチューニングします。

![Spark UI Executors](/images/databricks-cost-optimization-tips/spark-ui-executors.png)

Executorが全く起動していない場合は、シングルノードクラスターに変更するチャンスです。Sparkを利用していない、もしくは利用する必要がないジョブはシングルノードクラスターにすることで、さらにコスト最適化できます。

Databricks Asset Bundlesでは以下のように設定できます。

```yaml
resources:
  jobs:
    example_job:
      job_clusters:
        - job_cluster_key: "single_node_cluster"
          new_cluster:
            num_workers: 0
            spark_conf:
              spark.databricks.cluster.profile: singleNode
              spark.master: "local[*]"
            custom_tags:
              ResourceClass: SingleNode
```

## サーバレス固有の対応

### パフォーマンス最適化機能の無効化

サーバレスコンピュートには、クエリを高速化するための最適化機能がありますが、追加コストが発生します。バッチ処理など、速度よりもコスト効率を重視するケースでは無効化を検討してください。

Databricks Asset Bundlesでは以下のように設定できます。

```yaml
resources:
  jobs:
    example_job:
      tasks:
        - task_key: "serverless_task"
          notebook_task:
            notebook_path: "/path/to/notebook"
          environment_key: "default"
      # サーバレスのパフォーマンス最適化を無効化
      performance_target: "STANDARD"
```

UIでは以下の設定箇所から変更できます。

![サーバレスパフォーマンス設定](/images/databricks-cost-optimization-tips/serverless-performance-target.png)

# 削減効果

これらの施策により、DatabricksとAWSの料金を合わせて**半分以上のコスト削減**を実現できました。

![Databricksコスト最適化](/images/databricks-cost-optimization-tips/cost_optimize.png)

見てください！！上記はDatabricksのコスト削減のみ掲載していますが、IVRyはボルダリングウォールがある会社でして、見事にここでも壁を実現できるぐらい大幅なコストカットをしました！

# まとめ

本記事では、BigQueryからDatabricksへ移行後に直面したコスト増加の課題に対し、実際に行った最適化施策をご紹介しました。

**クラシックコンピュートの最適化**では、Workerノードへのスポットインスタンス活用、メトリクスに基づくクラスターサイズの見直し、r6g系へのクラスタータイプ変更、そしてSpark UIを確認しながらのワーカー数最適化やシングルノード化を実施しました。

**サーバレスの最適化**では、パフォーマンス最適化機能を無効化しました。また**共通の対応**として、Photon機能の利用を停止しました。

これらの施策により、DatabricksとAWSの料金を合わせて**半分以上のコスト削減**を実現できました。

コスト最適化は一度行えば終わりではなく、継続的な監視と改善が重要です。この記事がDatabricksのコスト削減に取り組む方の参考になれば幸いです。
