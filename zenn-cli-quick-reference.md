# Zenn CLI クイックリファレンス

## 記事の作成

### 基本コマンド

```bash
# 新しい記事を作成（ランダムなスラッグで生成）
npx zenn new:article
```

生成場所: `articles/` ディレクトリ

### オプション付きで作成

```bash
# スラッグ、タイトル、タイプ、絵文字を指定
npx zenn new:article --slug 記事のスラッグ --title タイトル --type idea --emoji ✨
```

**スラッグの制約:**
- 使用可能文字: `a-z0-9`、ハイフン `-`、アンダースコア `_`
- 文字数: 12～50字

### Front Matter 設定例

```yaml
---
title: "記事タイトル"
emoji: "📝"
type: "tech" # tech または idea
topics: ["zenn", "markdown"]
published: false # true で公開
published_at: 2050-06-12 09:03 # 公開予約（オプション）
---
```

## ローカルプレビュー

### 基本コマンド

```bash
# デフォルトポート（8000）で起動
npx zenn preview
```

ブラウザで `http://localhost:8000` にアクセス

### オプション

```bash
# ポート番号を指定
npx zenn preview --port 3000

# ファイル監視を無効化
npx zenn preview --no-watch
```

## 記事の公開

### 通常の公開

1. Front Matter の `published` を `true` に設定

```yaml
published: true
```

2. Git にコミット＆プッシュ

```bash
git add .
git commit -m "記事を公開"
git push origin main
```

連携済みリポジトリの登録ブランチへのプッシュで自動デプロイ開始

### 公開予約

将来の日時で公開する場合:

```yaml
published: true
published_at: 2050-06-12 09:03  # YYYY-MM-DD または YYYY-MM-DD hh:mm
```

- タイムゾーン: JST（日本時間）
- 指定した日時に自動公開

### 過去日時での公開

他サービスからの移行時などに使用:

```yaml
published: true
published_at: 2010-01-01 08:00
```

**注意:** 公開日時の指定は一度限りで、後から変更不可

## よく使うコマンドまとめ

```bash
# 記事作成
npx zenn new:article

# プレビュー起動
npx zenn preview

# 記事作成（詳細指定）
npx zenn new:article --slug my-first-article --title "はじめての記事" --type tech --emoji 🚀
```
