---
title: "GASでTwitterフォロワーリストを取得したい"
emoji: "👹"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["api", "googleappscrip", "googlespreadshe"]
published: true
---
# これは何
GASを使ってTwitterのフォロワーを取得、ユーザーネームとIDをスプレッドシートに全部ぶちこむ！！！！！！！！！！！！！！
をちょろっと仕事で依頼されたので備忘録程度にまとめています。

# ハマった所
https://taka-2.hatenablog.jp/entry/20110106/p1
こちらの記事を見ながらfollowingとfollower違うだけやしサクッといけるやろ！と思っていたのですが`oAuthConfig` がサポート終了していて使えなかったので`getOptions_()` 自体が死んでましたｶﾅｼﾐ🥲

なんとGoogle App Scriptでサポート終了していたんですね（知らんかった）
https://kijtra.com/article/twitter-api-for-google-apps-script-without-oauthconfig/

# 最終形
@[gist](https://gist.github.com/harunonsystem/a84164b8535f09dcf4662d38b63c18a9)

## 全体の流れ

###  1. APIキー関連を入れます（取得方法はググってください）
```
    var CONSUMER_KEY ="";
    var CONSUMER_SECRET = "";
```

---

### 2. 認証用URLを発行する必要があるので`getOAuthURL()`をGASで実行します。

```
// 認証用URL取得
function getOAuthURL() {
  Logger.log(getService().authorize());
}
```

https://developer.twitter.com/ での設定方法などは

こういう記事がいくらでもあるので
https://tech-cci.io/archives/4228

callback url はこちらを参考に。
```
https://script.google.com/macros/d/[GASのスクリプトID]/usercallback
```

Twitter認証が通ったら後はスクリプトを実行するだけです！

---

### 3. `apitest()`の実行
*~~※apitest とかいういかにもやってみた感じの名前にする必要はないです~~*

必要なものは
  - フォロワーリストを取得したいユーザーのID（ScreenName）
  - スプレッドシートに入れるシートの名前
の2つ。

ここでIDを入れます。（`harunonsystem` とかね）
```
  // *アカウントのID入れる（@なしのやつ）*
  var screenName = ""
```

事前にシートを作っておいて、ここでシートの名前を入れます。
```
  // スプレッドシートへの書き込み準備
  // *シートの名前を入れる*
  var range= SpreadsheetApp.getActiveSpreadsheet().getSheetByName("").getRange(1,1);
```
デフォルトの`シート1` のままでよければこうなります
![](https://storage.googleapis.com/zenn-user-upload/uiznzo3f67lf0s9lr8hekan5uxrz)

```
  var range= SpreadsheetApp.getActiveSpreadsheet().getSheetByName("シート1").getRange(1,1);
```
あとはAPIの呼び出しをして
```
// API呼び出し最大10回(MAX1,000件)で強制終了
  for(var page=1; page<=10; page++)
  {
    var url = "https://api.twitter.com/1.1/followers/list.json"+"?cursor=" + nextCursor +"&screen_name=" +screenName +"&count=200" ;
  
    // TwitterからFriendsの情報を取得
    var response = UrlFetchApp.fetch(url, { 'headers': { 'Authorization': 'Bearer ' + bearerTokens.access_token  } });
//  var resObj = Utilities.jsonParse(response.getContentText());
    var resObj = JSON.parse(response.getContentText());
    var users = resObj["users"];
    nextCursor = resObj["next_cursor_str"];
    Logger.log(nextCursor);
...
```

ループ回して1列目から順にScreenName、name, url, nextCursor, rownum, pageを入れています（ここは好きに入れると良いかと）
```
for(var i=0; i<users.length; i++) {
      var anStatus = users[i];
      var inscreenName = anStatus["screen_name"];
      var name = anStatus["name"];

      var rownum = i+1+(200*(page-1));

      range.offset(rownum, 0).setValue(inscreenName);
      range.offset(rownum, 1).setValue(name);
      range.offset(rownum, 2).setValue("https://twitter.com/" + inscreenName);
      range.offset(rownum, 3).setValue(nextCursor);
      range.offset(rownum, 4).setValue(rownum);
      range.offset(rownum, 5).setValue(page);
      Logger.log(rownum)
      
    }
```
- `nextCursor` はページネーションの役割になるので、直前に`-1`を指定しています
- `rownum` は取得しているフォロワー数が合っているかの確認のために入れてました。行番号+1になります。
- `page` はAPI呼び出しているときのページ数です。

`-1`スタートを起点に0になるまでループを回します。
```
if(nextCursor === "0")
    {
      break;
    }
```

あとはスプレッドシートに記載されるのを待つだけです。

途中、Rate limitに悩まされることがあった（取得するフォロワーが1万人以上いると特に💦）ので
`Utilities.sleep(30000);` を挟んだりしています。

ってな感じで取得できました🎉🎉

![](https://storage.googleapis.com/zenn-user-upload/2wzko2ctpwagazf4mx2xk7h4ng62)


もっと良い書き方があれば教えて下さい…><