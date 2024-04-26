---
title: "iPhoneのショートカットでバッテリー残量が減ってきたとき充電を促す"
emoji: "🎃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["iphone", "iphoneオートメーション"]
published: false
---

# これは何

iPhoneやAirPodsの充電が少なくなってても気づかないことがありますよね。
そんなときに、バッテリー残量が減ってきたときに自動で充電を促すショートカットを作ります。
（充電してるはずが置きっ放しで出かけるときに5%しかなかった…みたいなことがなくなるかもです）

# 今回のゴール

![](https://storage.googleapis.com/zenn-user-upload/f5487f84aba8-20240406.png)

こんな感じのを作ります。
iPhoneの充電だけであれば、iPhoneのみで完結します。
AirPodsの場合は別のアプリも使うので後述します。どちらもノーコードです。

「どっかで何かします」部分は、別になんでもいいので自分が気づきやすいモノに置き換えてましょう。私はMisskeyに投稿してます。
↓↓こんな感じに↓↓
![](https://storage.googleapis.com/zenn-user-upload/5afadf5ffcbe-20240406.png)

# iPhoneのみ編

## やり方

全体はこんな感じの流れです。
![](https://storage.googleapis.com/zenn-user-upload/18b64f0524b5-20240407.png)

### ショートカットアプリを開く

まずiPhoneでショートカットアプリを開きます。こういうやつ。
「ショートカット」で検索すれば出てきます。
![](https://storage.googleapis.com/zenn-user-upload/f5bbd96ce034-20240406.png)

で、これを開いて一番下のタブを「ショートカット」→「オートメーション」に移動して
右上の「+」をタップします。
![](https://storage.googleapis.com/zenn-user-upload/d8e0d6489a48-20240406.jpg)

### 個人用オートメーション作成

個人用オートメーションが出てくるので、
![](https://storage.googleapis.com/zenn-user-upload/b802c3c00a3e-20240406.jpg)

### バッテリーを探す

「バッテリー」と検索します。
バッテリー残量が出てきたらこれをタップ。
![](https://storage.googleapis.com/zenn-user-upload/0d09b48f5891-20240406.jpg)

#### バッテリー残量の設定

今回はバッテリー残量が30%以下になったら充電を促したいので、
「バッテリー残量が30%未満になったとき」に設定をします。
「すぐに実行」「実行後に通知」をONにしておくと勝手に実行されるので便利です。
終わったら「次へ」をタップします。
![](https://storage.googleapis.com/zenn-user-upload/421bfecbee01-20240406.jpg)

### 通知を設定

こんな画面になります。
「いつ？→バッテリー残量が30%未満になったとき」の設定が終わったので、「何をする？」の部分を作りましょう。
とりあえず「通知」と検索してみます。
![](https://storage.googleapis.com/zenn-user-upload/959a817d5d2c-20240406.jpg)

「通知を表示」が出てくるのでタップします。
![](https://storage.googleapis.com/zenn-user-upload/8d4a05ad1c99-20240406.jpg)

#### 通知の詳細を設定

「通知を表示」の設定画面が出てきます。
![](https://storage.googleapis.com/zenn-user-upload/6aee79aa0aed-20240406.jpg)

「タイトル」に「充電してください」とか「バッテリーが30%未満です」とか入れておくといいかもです。
終わったら「完了」をタップします。
![](https://storage.googleapis.com/zenn-user-upload/8c5b28ccc1a9-20240406.jpg)

### 完成🎉

「オートメーション」の画面に戻ってきます。これでiPhone側は完了です。
「iPhoneのバッテリー残量が30%未満になったときに通知を表示する」というオートメーションが作成されました。🎉
![](https://storage.googleapis.com/zenn-user-upload/2111dede94c5-20240406.jpg)
タップすると、こんな感じに詳細が確認できるので、いつでも編集できます。
![](https://storage.googleapis.com/zenn-user-upload/a08020996045-20240406.jpg)

# AirPods編

AirPodsの場合は、バッテリー残量取得がiPhoneだけではできない（新しいAirPods2022とかなんとかすればできるのかもしれないけど）ので、別のアプリを使います。

:::message alert
そういうやり方をRedditで見つけて上手く行っただけなので、利用は自己責任でお願いします。
:::

## AirPodsのバッテリー残量を取得するアプリをインストール

AllMyBatteriesというアプリをインストールします。
https://apps.apple.com/us/app/allmybatteries/id1621263412

なんか設定するとバッテリー残量が取得できるらしいです。
![](https://storage.googleapis.com/zenn-user-upload/3fb6325f8adb-20240406.jpg)

## やり方

さぁ、こっからがちょっと面倒です。

### ショートカットを新規作成します。

一番下のタブ「ショートカット」の状態で、右上の「+」をタップします。
![](https://storage.googleapis.com/zenn-user-upload/663768bf78bb-20240406.jpg)

### if文作成

画面下の「アプリおよびアクションを検索」をタップします。
![](https://storage.googleapis.com/zenn-user-upload/865e4f9baa6f-20240406.jpg)

#### Force Sync 作成

画面上のタブ、カテゴリ→アプリに移動。
AllMyBatteriesを選択します。
![](https://storage.googleapis.com/zenn-user-upload/2f6eec69f657-20240406.jpg)

Force syncを選択します。
![](https://storage.googleapis.com/zenn-user-upload/63e8d1dae244-20240406.jpg)

#### Get batter level of deviceの設定

if文の画面に戻ってきました。
もう1回、下の「アプリおよびアクションを検索」をタップしてAllMyBatteriesを選択します。
![](https://storage.googleapis.com/zenn-user-upload/400ff6609574-20240406.jpg)
![](https://storage.googleapis.com/zenn-user-upload/2f6eec69f657-20240406.jpg)

次は、Get batter level of device を選択します。
![](https://storage.googleapis.com/zenn-user-upload/de2d6a5bba91-20240406.jpg)

Deviceを選択します
![](https://storage.googleapis.com/zenn-user-upload/ffedeee8d34d-20240406.jpg)

AirPodsを選択します
![](https://storage.googleapis.com/zenn-user-upload/8093d0bf05a6-20240406.jpg)

if文の上に持っていきます
![](https://storage.googleapis.com/zenn-user-upload/5ea480993a0d-20240406.jpg)

#### 通知を設定

そしたらまた、下の「アプリおよびアクションを検索」をタップして**通知**を検索して選択します。
「通知を表示」をタップします。
![](https://storage.googleapis.com/zenn-user-upload/9a09c51152b3-20240406.jpg)

こんな感じです。
![](https://storage.googleapis.com/zenn-user-upload/18070690bf7c-20240406.jpg)

#### if文の中身を調整

if文の中身はこちら。AllMyBatteriesの変数が選べるようになるので、以下のように値を入れていきます

- Battery Level
- が次より小さい
- 30
  ![](https://storage.googleapis.com/zenn-user-upload/73f011947728-20240406.jpg)

ここで一旦右上の「完了」をタップします。

### オートメーション作成

「オートメーション」開いて、「+」をタップします。
![](https://storage.googleapis.com/zenn-user-upload/d8e0d6489a48-20240406.jpg)

#### 「充電されたときに」を設定

「充電」と検索して、「充電器」をタップします。
![](https://storage.googleapis.com/zenn-user-upload/43029101ff7b-20240406.jpg)

#### 充電されたときに「何をする」かを設定

先ほど作った「ショートカット」（名前変えてなければ「if文」のままのはず）をタップします。
![](https://storage.googleapis.com/zenn-user-upload/d875082c5cd5-20240406.jpg)

### 完成🎉

これで完成です。
![](https://storage.googleapis.com/zenn-user-upload/78ff9bceeceb-20240406.jpg)

タップしたら詳細こんな感じです。
![](https://storage.googleapis.com/zenn-user-upload/9235158c13a6-20240406.jpg)

これで、「iPhoneを充電したときにAirPodsのバッテリー残量が30%以下だったら通知する」という設定ができました。おめでとうございます🎉
