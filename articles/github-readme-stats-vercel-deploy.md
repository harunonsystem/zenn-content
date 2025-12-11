---
title: "github-readme-statsを自分用にForkしてVercel連携して使う方法"
emoji: "👾"
type: "tech"
topics: ["github", "vercel", "readme", "cloudflare"]
published: true
---

github-readme-stats、こういうやつです。

:::message
[追記]
自分のusernameのみ利用を許可する方法として、環境変数 `WHITELIST` を使う手順を追記しました。
:::

![](https://storage.googleapis.com/zenn-user-upload/cddcbb59967a-20251206.png)

最近Issueで報告も上がってましたが、表示されなくなっていたので自分用にVercelで立てて使ってみました。
([github-readme-stats not working? · Issue #4658](https://github.com/anuraghazra/github-readme-stats/issues/4658))

基本、GitHubでForkして、VercelでDeployするだけです。

## 流れ

1. GitHubでリポジトリをForkする
2. GitHub PAT (Personal Access Token) を取得する
3. VercelにDeployする（環境変数 `PAT_1` を設定）
4. (Optional) 自分以外の利用を制限する
   - 4-1. 環境変数で制限する（推奨）
   - 4-2. Cloudflareで制限する
5. READMEに貼り付ける

## 1. GitHubでForkする

まずは本家のリポジトリを自分のアカウントにForkします。

[anuraghazra/github-readme-stats](https://github.com/anuraghazra/github-readme-stats)

リポジトリページの右上にある「Fork」ボタンを押して、自分のアカウントにコピーを作成します。

![](https://storage.googleapis.com/zenn-user-upload/7bc79d7a4fc4-20251206.png)

## 2. GitHub PATを取得する

1. GitHubの **Settings** > **Developer settings** > **Personal access tokens** > **Fine-grained Token** にアクセス
2. "Generate new token" をクリック

生成されたトークンは一度しか表示されないのでコピーしておきます。

[https://github.com/settings/personal-access-tokens](https://github.com/settings/personal-access-tokens)

![](https://storage.googleapis.com/zenn-user-upload/9fd911b8ebd7-20251206.png)

## 3. VercelでDeployする

Vercelにログインして、"Add New..." -> "Project" から作業を開始します。
[Vercel Add New](https://vercel.com/new)

1. **Import Git Repository** で、先ほどForkした `github-readme-stats` を選択してImportします。
2. **Configure Project** の画面で、**Environment Variables**（環境変数）を開きます。
3. 以下の値を設定します：
   - **Key**: `PAT_1`
   - **Value**: (手順2でコピーしたGitHub PAT)
4. "Add" を押して変数を追加し、最後に "Deploy" をクリックします。


![](https://storage.googleapis.com/zenn-user-upload/08e91566cb96-20251206.png)

デプロイが完了すると、`https://github-readme-stats-xxxxx.vercel.app` のようなドメインが発行されます。

## 4. (Optional) 自分以外の利用を制限する

自分以外のユーザーがあなたのデプロイしたインスタンスを利用しないように制限をかけます。

### 4-1. 環境変数で制限する（推奨）

VercelのEnvironment Variablesで `WHITELIST` を設定することで、特定のユーザーのみに利用を制限できます。

1. Vercelのプロジェクト設定（**Settings** > **Environment Variables**）を開く
2. 以下の変数を追加する：
   - **Key**: `WHITELIST`
   - **Value**: (自分のGitHubユーザー名)

カンマ区切りで複数のユーザーを指定することも可能です。
これを設定すると、リストに含まれないユーザーのリクエストはブロックされるようになります。

### 4-2. Cloudflareで制限する

:::message
この記事の初稿で紹介していた方法です。
Cloudflareの設定に慣れている方や、より柔軟な制限（Reffer制限など）をかけたい場合に使用してください。
:::

READMEで使用する仕様上、deployされたvercel.appのサブドメインが公開されてしまうため、自分以外のユーザー名ではブロックする設定をCloudflareで追加します。

#### 準備: ドメインとDNSの設定

VercelのデフォルトドメインにはCloudflareへの設定を適用できないため、以下の構成にします。

1. **ドメイン**: Cloudflare Registrarなどで取得し、DNSをCloudflareで管理する
2. **DNSレコード**: Vercelに向けたCNAMEレコードを作成し、**Proxy (オレンジ色の雲マーク)** をONにする
   - Type: `CNAME`
   - Name: `stats` (例)
   - Target: `cname.vercel-dns.com`
   - Proxy status: **Proxied**

これでDNS側の設定は完了です。

![](https://storage.googleapis.com/zenn-user-upload/818f1b59f56c-20251206.png)

#### 設定手順: Custom Rules

Cloudflareのダッシュボードから対象ドメインを選び、**Security** > **Security Rules** の **Custom rules** タブから「Create rule」に進みます。


1. ルールを作成→カスタムルール
![](https://storage.googleapis.com/zenn-user-upload/fa72520392d4-20251206.png)
2. 式を編集
![](https://storage.googleapis.com/zenn-user-upload/425902a6ad50-20251206.png)
3. ルール名、式、アクション、場所を設定してデプロイ（右下のボタン）
![](https://storage.googleapis.com/zenn-user-upload/547db73035a5-20251206.png)

---
ルール名：（例）`block-other-users-stats`

式:(`YOUR_DOMAIN`は設定したドメイン名、`YOUR_USERNAME`は自分のGitHubユーザー名)
```text
(http.request.uri.path contains "/api")
and http.host eq "[YOUR_DOMAIN]"
and
not (
  http.request.uri.query contains "username=[YOUR_USERNAME]"
)
```

アクション：ブロック

場所：最初

---


作成したサブドメイン経由で`https://[YOUR_DOMAIN]/api?username=anuraghazra`などにアクセスして、以下の画面になれば成功です。
（セキュリティルールを作成したドメイン経由で自分以外のユーザーネームを入れてアクセス→ブロックされる）
![](https://storage.googleapis.com/zenn-user-upload/0ad62b12d49f-20251206.png)


## 5. READMEに追加する

あとはGitHubのREADMEで、`github-readme-stats` のURL（`https://github-readme-stats.vercel.app`）を、自分のVercelのドメインに置き換えてREADMEに貼り付けるだけです。

`github-readme-stats.vercel.app` を `[YOUR_DOMAIN]` に置き換えてください。

ちなみに私のはこちらです。（一番最後に載せてあります。）
https://github.com/harunonsystem/harunonsystem/commit/e60576842c796174dc0f1bda0ff97297a4e99bda

