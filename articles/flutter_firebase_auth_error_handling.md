---
title: "Flutter x Firebase AuthのEmail認証で使うエラーハンドリング"
emoji: "😘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["flutter", "firebase"]
published: true
---

# 今回やること
FlutterとFirebaseAuthを使ったEmail認証のエラーハンドリングをします。
認証エラーが発生したときに、どのエラーなのかを確認し、エラーごとのメッセージをユーザーに伝えられるようにします。

最終的にこういうものができます。
![](https://storage.googleapis.com/zenn-user-upload/a92421481a135b287f5d215e.gif)

流れとしては、
1. FlutterとFirebaseを繋げる（ググればたくさん出てくるのでできている前提で割愛します）
2. LoginPageと Functionを作成
3. LoginPageでEmailの認証を行う
4. SignInEmail Functionで出てきたErrorCodeからError Handlingを行う
5. Error Messageを表示する

# 事前準備
- Flutter プロジェクト作成済。
- Firebase アプリ作成済。
- Firebase アプリにEmail認証を設定している。（Flutterで使えるようトグルをONに）

```dart: pubspec.yaml
  // 導入するパッケージ
  firebase_auth: ^1.4.1
  firebase_core: ^1.3.0
```

### 作成するファイル
```dart:lib/
  login.dart
  firebase_auth_error.dart
```            

# MyHomePageにLoginPage遷移するBtnを追加
Flutterプロジェクトを作成したときのカウンターアプリの中に、ログインページへ遷移させるBtnを追加します。

![](https://storage.googleapis.com/zenn-user-upload/56b26534dff05e0de2bd0bd5.png) 
こういう画面を作ります。
```dart: main.dart
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              Text(
                'You have pushed the button this many times:',
              ),
              Text(
                '$_counter',
                style: Theme.of(context).textTheme.headline4,
              ),
        //add button
              ElevatedButton(
                onPressed: () => Navigator.of(context)
                    .push(MaterialPageRoute(builder: (ctxt) => LoginPage())),
                child: Text('login page'),
              )
            ],
          ),
        ),
```


# Login Functionを作成
`login.dart` を作成します。（ `main.dart` の `MyHomePage` 部分をLoginPageとして変更しても良いですが、Build→ログイン画面に遷移、がわかりやすいと思ったのであえて分割しています。）

Login Functionは、ログインページへ遷移するボタンを押したときに実行される処理です。
Email認証でしか使わないので、`signInEmail` のみ。

`FirebaseAuthResultStatus` はError Handlingで使うので後述します。

```dart:login.dart
  Future<FirebaseAuthResultStatus> signInEmail(
      String email, String password) async {
    FirebaseAuthResultStatus result;
    try {
      UserCredential userCredential = await auth.signInWithEmailAndPassword(
          email: email, password: password);
      print('succeed');
      if (userCredential.user! != null) {
        result = FirebaseAuthResultStatus.Successful;
      } else {
        result = FirebaseAuthResultStatus.Undefined;
      }
    } on FirebaseAuthException catch (e) {
      print(e.code);
      result = FirebaseAuthExceptionHandler.handleException(e);
    }
    return result;
  }
```

### Error Handlingを作成
Error Handlingは、Login Pageと別ファイルにするので、`firebase_auth_error.dart` を作成します。

1. FirebaseAuthから発生する例外をenumで定義
2. 例外を捕捉し、codeから1で宣言した型を返す
3. Error Message作成

#### enum定義
```dart:firebase_auth_error.dart
enum FirebaseAuthResultStatus {
  Successful,
  EmailAlreadyExists,
  WrongPassword,
  InvalidEmail,
  UserNotFound,
  UserDisabled,
  OperationNotAllowed,
  TooManyRequests,
  Undefined,
}
```

#### 宣言した型を返す
```dart:firebase_auth_error.dart
FirebaseAuthResultStatus handleException(FirebaseAuthException e) {
  FirebaseAuthResultStatus result;
  switch (e.code) {
    case 'invalid-email':
      result = FirebaseAuthResultStatus.InvalidEmail;
      break;
    case 'wrong-password':
      result = FirebaseAuthResultStatus.WrongPassword;
      break;
    case 'user-disabled':
      result = FirebaseAuthResultStatus.UserDisabled;
      break;
    case 'user-not-found':
      result = FirebaseAuthResultStatus.UserNotFound;
      break;
    case 'operation-not-allowed':
      result = FirebaseAuthResultStatus.OperationNotAllowed;
      break;
    case 'too-many-requests':
      result = FirebaseAuthResultStatus.TooManyRequests;
      break;
    case 'email-already-exists':
      result = FirebaseAuthResultStatus.EmailAlreadyExists;
      break;
    default:
      result = FirebaseAuthResultStatus.Undefined;
  }
  return result;
}
```

#### Error Message作成
```dart:firebase_auth_error.dart
String exceptionMessage(FirebaseAuthResultStatus result) {
  String? message = '';
  switch (result) {
    case FirebaseAuthResultStatus.Successful:
      message = 'ログインに成功しました。';
      break;
    case FirebaseAuthResultStatus.EmailAlreadyExists:
      message = '指定されたメールアドレスは既に使用されています。';
      break;
    case FirebaseAuthResultStatus.WrongPassword:
      message = 'パスワードが違います。';
      break;
    case FirebaseAuthResultStatus.InvalidEmail:
      message = 'メールアドレスが不正です。';
      break;
    case FirebaseAuthResultStatus.UserNotFound:
      message = '指定されたユーザーは存在しません。';
      break;
    case FirebaseAuthResultStatus.UserDisabled:
      message = '指定されたユーザーは無効です。';
      break;
    case FirebaseAuthResultStatus.OperationNotAllowed:
      message = '指定されたユーザーはこの操作を許可していません。';
      break;
    case FirebaseAuthResultStatus.TooManyRequests:
      message = '指定されたユーザーはこの操作を許可していません。';
      break;
    case FirebaseAuthResultStatus.Undefined:
      message = '不明なエラーが発生しました。';
      break;
  }
  return message;
}
```
Error Messageはサンプルなので適宜変更しましょう。

# Login PageでError Messageと入力フォームを追加する
Error Message
----
`signInEmail` でError を返した場合、Error Messageを表示します。
メッセージ自体は`firebase_auth_error.dart` の`ExceptionMessage` 作成しているので、
メッセージを表示するダイアログをここで作成しておきます。

```dart:login.dart
    void _showErrorDialog(BuildContext context, String? message) {
    showDialog(
      context: context,
      barrierDismissible: false,
      builder: (BuildContext dialogContext) {
        return AlertDialog(
          title: Text(message!),
          actions: <Widget>[
            TextButton(
              child: Text('OK'),
              onPressed: () {
                Navigator.pop(dialogContext);
              },
            ),
          ],
        );
      },
    );
  }
  ```

  
  ----

  Scaffold以下のUI部分
  ----
  EmailとPasswordの入力フォームなど画面全体のUIを作成します。

  ```dart:login.dart
  @override
  Widget build(BuildContext context) {
    final _formKey = GlobalKey<FormState>();
    final _formPWKey = GlobalKey<FormState>();

    TextEditingController _email = new TextEditingController();
    TextEditingController _password = new TextEditingController();
    return Scaffold(
      appBar: AppBar(
        title: Text('Login'),
      ),
      body: Center(
        child: Column(children: [
          Padding(
            padding: const EdgeInsets.all(8.0),
            child: Form(
                key: _formKey,
                child: TextFormField(
                  controller: _email,
                  decoration: InputDecoration(
                    labelText: 'Email',
                    border: OutlineInputBorder(
                        borderRadius: BorderRadius.circular(8.0)),
                    hintText: 'Email',
                  ),
                )),
          ),
          Padding(
            padding: const EdgeInsets.all(8.0),
            child: Form(
                key: _formPWKey,
                child: TextFormField(
                  controller: _password,
                  obscureText: true,
                  decoration: InputDecoration(
                    labelText: 'password',
                    border: OutlineInputBorder(
                        borderRadius: BorderRadius.circular(8.0)),
                    hintText: 'password',
                  ),
                )),
          ),
          TextButton(
            style: TextButton.styleFrom(
              backgroundColor: Colors.blue,
              elevation: 1.0,
            ),
            child: Text(
              'login',
              style: TextStyle(color: Colors.white),
            ),
            onPressed: () async {
              _formKey.currentState!.validate();
              _formPWKey.currentState!.validate();

              final FirebaseAuthResultStatus signInResult =
                  await signInEmail(_email.text, _password.text);
              if (signInResult != FirebaseAuthResultStatus.Successful) {
                final errorMessage =
                    FirebaseAuthExceptionHandler.exceptionMessage(signInResult);

                _showErrorDialog(context, errorMessage);
              } else {
                ScaffoldMessenger.of(context).showSnackBar(
                    const SnackBar(content: Text('Login successful')));
                Navigator.push(
                    context,
                    MaterialPageRoute(
                        builder: (context) =>
                            MyHomePage(title: 'My Home Page')));
              }
            },
          ),
        ]),
      ),
    );
  }
```

ErrorMessage例
----

| wrong-password | too-many-requests |
|--|--|
|![](https://storage.googleapis.com/zenn-user-upload/dee6ea2ae6e7a666767fb09c.png) | ![](https://storage.googleapis.com/zenn-user-upload/aeaa3f87cae58aa5ddb9561a.png) |

## 最終的なコード
https://github.com/harunonsystem/firebase-auth-error
