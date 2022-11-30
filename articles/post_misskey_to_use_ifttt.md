---
title: "IFTTTを利用してSpotifyでいいねした曲をMisskeyに投稿する"
emoji: "🎶"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ['ifttt', 'spotify', 'misskey']
published: true
published_at: 2022-12-05 
---
:::message
この記事は、[Misskeyアドベントカレンダー](https://adventar.org/calendars/7354) の5日目の記事です。
:::

# これは何

タイトル通りですが、IFTTTを利用してSpotifyでいいねした曲をMisskeyに自動投稿する方法を紹介します。

# 事前準備

## アカウントを作成しておくもの
すべてアカウント作成が済んでいれば、ここはスキップしてしまっても大丈夫です。
  - [Spotify](https://www.spotify.com/jp/)
  - [Misskey](https://misskey.io/)
  - [IFTTT](https://ifttt.com/)

Misskey に関してはMisskey.io が一番有名かもしれませんが[インスタンス一覧](https://join.misskey.page/ja-JP/instances) にあるインスタンスであれば大丈夫と思われます。

# IFTTTでアプレットを作成する

IFTTT にログインして、アプレットを作成します。

[新規アプレットの作成](https://ifttt.com/create)をクリックすると以下のような画面になります。

![](https://storage.googleapis.com/zenn-user-upload/7f3339721e06-20221201.png)

画面中央の「If This」と 「Then That」をクリックして、それぞれ以下のように設定します。

## If This

If Thisをクリックすると、以下のような画面になります。
![](https://storage.googleapis.com/zenn-user-upload/057916206a5b-20221201.png)

中央の検索バーからSpotifyを検索、選択します。
一番左がSpotifyになりますので、クリックします。

※ ここでSpotifyのアカウントを連携する必要があります。

![](https://storage.googleapis.com/zenn-user-upload/e6785c673ff7-20221201.png)

If Thisでは「Spotifyで何をしたときMisskeyに投稿するか」を設定します。
Spotify上で曲をいいねしたらMisskeyに投稿したいので、 上段右から2番目の「New saved track」を選択します。
![](https://storage.googleapis.com/zenn-user-upload/f03ea2cb4fb1-20221201.png)

こういう画面になると思います。
![](https://storage.googleapis.com/zenn-user-upload/69aca57cd3e8-20221201.png)


これで半分終わりました！(๑•̀ㅂ•́)و✧

## Misskeyのアクセストークンの発行

Then Thatで使うアクセストークンの発行をします。

Misskeyのアクセストークンは、設定画面から取得できます。

まず、Misskeyの設定画面に行きます。
左のメニューから「設定」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/cdb9f74041e3-20221201.png)

APIを選択します。
![](https://storage.googleapis.com/zenn-user-upload/8fd7915d6f33-20221201.png)

「アクセストークンの発行」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/99cb0b5bb169-20221201.png)

名前は任意で大丈夫ですが、下にスクロールして「ノートを作成・削除する」だけ許可します。
![](https://storage.googleapis.com/zenn-user-upload/d155a38c8a06-20221201.png)

テキスト左のスイッチを押して、ONにします。
![](https://storage.googleapis.com/zenn-user-upload/45e9c096707e-20221201.png)

アクセストークンが出てくるのでこれをコピーします。
IFTTTで使うので忘れないようにしてください。
![](https://storage.googleapis.com/zenn-user-upload/2a72a1c924bb-20221201.png)

## Then That

Then Thatをクリックすると、以下のような画面になります。
![](https://storage.googleapis.com/zenn-user-upload/057916206a5b-20221201.png)

中央の検索バーからWebhooksを検索、選択します。
中央がWebhooksになりますので、クリックします。
![](https://storage.googleapis.com/zenn-user-upload/ac8e1d992b62-20221201.png)

左の「Make a web request」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/1dea6ca20735-20221201.png)


こういう画面になると思います。
![](https://storage.googleapis.com/zenn-user-upload/2cfee64d5397-20221201.png)


`URL`には、Misskeyの投稿用APIを指定します。
Misskeyの投稿用APIは、インスタンスのURLに/api/notes/create を付けたものになります。
例えば、Misskey.ioの場合は、`https://misskey.io/api/notes/create` となります。

`Method`は、POSTにします。

`Content Type`は、application/jsonにします。

こんな感じになります。
![](https://storage.googleapis.com/zenn-user-upload/149d2f4a21a8-20221201.png)

`Body`は、以下のようにします。

```json
{
  "i": "YOUR_MISSKEY_TOKEN",
  "text": "ここに本文が入る",
  "visibility": "home",
}
```
- `i`は、Misskeyのアクセストークンを入れます。
- `text`は、ノートされる内容が入ります。
- `visibility`は、投稿の公開範囲です。homeにすると、自分のホームタイムラインにのみ表示されます。

visibilityは、`public`, `home`, `followers`, `specified` が指定できます。

他にも投稿内容をカスタマイズできるので、気になる人は[こちら](https://misskey.io/api-doc#operation/notes/create)を参照してください。

### textに曲名やアーティスト名を入れる

Then Thatで、Spotifyの曲名やアーティスト名をMisskeyに投稿するには、SpotifyのWebhooksで取得した情報をThen Thatに渡す必要があります。

以下のように、textにカーソルを合わせてから、Add Ingredientをクリックします。
`TrackName`を選択すれば、曲名が、`ArtistName`を選択すれば、アーティスト名が入ります。
`\n` は改行を表します。
![](https://storage.googleapis.com/zenn-user-upload/a83e6168d668-20221201.gif)

最後に、`Create action`をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/282149a236ca-20221201.png)

これで、Then Thatの設定は完了です。

ここまで来たら、「Spotifyで曲にいいねしたら、Misskeyに投稿される」が自動で作成されているはずです。

いいねを押したら、
![](https://storage.googleapis.com/zenn-user-upload/1d1677c4d83c-20221201.png)

投稿されます🎉
![](https://storage.googleapis.com/zenn-user-upload/e3bae91b8432-20221201.png)

### 聖地巡礼
https://misskey.io/notes/98711k94oa

# まとめ
IFTTTでWebhookを使うとMisskeyに自動投稿できるので、個人でも色々試せると思います。
IFTTTとWebhookのやり方さえわかれば、他のサービスとの連携もできると思います。

今回、滑り込みの初アドカレ初Misskey関連投稿でしたが、なんとか無事に終わりました。
今後もWebhook使えば色々できそうなので空いた時間でやるかもしれません。そのときはまたお会いしましょう。

# 参考
https://qiita.com/hir-osechi/items/5e61b7701a98f5084772
https://zenn.dev/meziro/articles/f584843818f150#%E3%83%8E%E3%83%BC%E3%83%88%E3%82%92%E6%8A%95%E7%A8%BF%E3%81%99%E3%82%8B
https://misskey-hub.net/docs/api/endpoints/notes/create.html