---
title: "Umami AnalyticsをVercelでデプロイして使う方法"
emoji: "📈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["analytics", "umami", "vercel", "seo"]
published: true
---

# Umamiとは

Umamiは、Google Analyticsの代替となるオープンソースのウェブアナリティクスツールです。プライバシーを重視した設計で、Cookieを使用せず、GDPRに準拠しています。

主な特徴：
- シンプルで使いやすいダッシュボード
- リアルタイムのアクセス解析
- ページビュー数、訪問者数、参照元などの基本的な指標
- 軽量で高速

Google Analyticsのように詳細なトラッキングはできませんが、ブログや小規模サイトのアクセス解析には十分な機能を備えています。

# 今回やること

Umami AnalyticsをVercelでデプロイして、自分のサイトのアクセス解析を始められるようにします。

最終的に、以下のようなダッシュボードでアクセス統計を見られるようになります。

![](https://storage.googleapis.com/zenn-user-upload/example-umami-dashboard.png)

流れとしては、
1. Umamiのリポジトリをフォーク
2. Vercelでプロジェクトを作成
3. データベースを設定
4. 環境変数を設定してデプロイ
5. 初期設定をして使用開始

# 事前準備

- GitHubアカウント
- Vercelアカウント（GitHubで連携可能）
- PostgreSQLデータベース（VercelのPostgresを使用）

# UmamiをVercelでデプロイする

## 1. Umamiのリポジトリをフォーク

まず、GitHubで[umami-software/umami](https://github.com/umami-software/umami)を自分のアカウントにフォークします。

## 2. Vercelでプロジェクトを作成

Vercelにログインし、「Add New...」→「Project」から先ほどフォークしたumamiリポジトリを選択します。
[Vercel Add New](https://vercel.com/new)
## 3. データベースの準備

Vercelのダッシュボードから「Storage」→「Create Database」→「Postgres」を選択してデータベースを作成します。

![](https://storage.googleapis.com/zenn-user-upload/5392295b3883-20251202.png)

Neonを選んでいます。

![](https://storage.googleapis.com/zenn-user-upload/fc43fbff2cc0-20251201.png)

作成されたデータベースの接続情報をコピーしておきます。
（Show secretをクリックすると見えます）

![](https://storage.googleapis.com/zenn-user-upload/cabb6840ec10-20251201.png)

## 4. 環境変数を設定

プロジェクトの設定で、以下の環境変数を追加します：

- `DATABASE_URL`: データベースの接続文字列（先ほどコピーしたもの）
- `HASH_SALT`: 任意の文字列（例: `your-random-salt`）
- `TRACKER_SCRIPT_NAME`: トラッカースクリプトの名前（例: `umami.js`）

## 5. デプロイ実行

環境変数の設定が完了したら、「Deploy」をクリックしてデプロイを開始します。

デプロイが完了すると、UmamiにVercelが提供するURLでアクセスできるようになります。

# 初期設定

デプロイ完了後、UmamiのURLにアクセスするとログインページが表示されます。

デフォルトのログイン情報：
- ユーザー名: `admin`
- パスワード: `umami`

ログイン後、まずパスワードを変更してください。

その後、「Settings」→「Websites」から解析したいサイトを追加します。サイト名とURLを入力するだけで設定完了です。

# トラッキングコードの設置

解析したいサイトのHTMLに、以下のスクリプトを追加します：

```html
<script
  defer
  src="https://your-umami-domain.com/umami.js"
  data-website-id="your-website-id"
></script>
```

`your-umami-domain.com`はデプロイしたUmamiのドメイン、`your-website-id`はUmamiの設定で確認できるウェブサイトIDに置き換えてください。

これで、サイトへのアクセスがUmamiで解析されるようになります。



# 補足

## カスタムドメインの設定

VercelのデフォルトURLではなく、自分のドメインを使いたい場合は、環境変数に`BASE_URL`を追加してください。

- `BASE_URL`: 自分のドメイン（例: `https://analytics.yourdomain.com`）

これを設定すると、トラッキングスクリプトのURLも自動的にカスタムドメインに変わります。



こんな感じに確認できます。
![](https://storage.googleapis.com/zenn-user-upload/d07cf4034fca-20251201.png)