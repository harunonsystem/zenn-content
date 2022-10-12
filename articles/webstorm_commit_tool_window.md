---
title: "JetBrains系 IDEで左端のツールウィンドウにコミットウインドウを出す"
emoji: "🐙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['webstorm', 'intellij']
published: true
---

# これは何
JetBrains系だと、左端に垂直ツールウィンドウというものが出てくると思います。


私の場合、「プロジェクト」「コミット」「プルリクエスト」の3つでした。（プラグイン色々入れてるせい…）
何も設定していない状態だと、「プロジェクト」のみ出てくるかもしれません。

![](https://storage.googleapis.com/zenn-user-upload/89681c5f903a-20221012.png)

この垂直ツールウィンドウでコミットタブを選択するとローカルで変更差分のあるファイルを見ながら開発をしてくことができるので便利で重宝しています。

しかし、この「コミット」がインストールした初期の状態で無いことがたまにあり、
どうやって出すのかに時間をかけてしまったので備忘録程度にまとめます( ᵕ̩̩ㅅᵕ̩̩ )


## それでは本題
`File` > `Settings` （[Macの場合] `WebStorm` > `Preferences`）を選択し、設定ウィジェットを開きます。

`Version Control` >`Commit` を選択し、`Use non-modal commit interface` にチェックを入れて保存します。
![](https://storage.googleapis.com/zenn-user-upload/2f1af8293a4a-20221012.png)

Ref:
https://rider-support.jetbrains.com/hc/en-us/community/posts/4403005856658/comments/4403033541522

### おまけ

名前がわからないもの調べるの苦労しますよね...
それでは良き開発ライフを！！