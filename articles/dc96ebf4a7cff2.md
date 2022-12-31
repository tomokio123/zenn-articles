---
title: "【Flutter】Timerを使った簡単な自動遷移の実装"
emoji: "🦔"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Flutter","Dart"]
published: true
---
# Timer自動遷移の実装
### サンプルコード
- dart:async を使ってTimerを設計していく
```dart:first_screen.dart
import 'dart:async';
import 'package:flutter/material.dart';

class FirstScreen extends StatefulWidget {
  const FirstScreen({Key? key}) : super(key: key);

  @override
  State<FirstScreen> createState() => _FirstScreenState();
}

class _FirstScreenState extends State<FirstScreen> {
  // ここでTimerを宣言する
  Timer? timer;
  @override
  void initState() {// 画面初期化時に呼ばれるメソッド
    super.initState();

    //Timerのインスタンスを作る
    //Timerの引数は遅延時間(Duration)と経過時間後の操作を記したコールバック関数の二つを搭載する
    timer = Timer(
      const Duration(seconds: 2),//遅延時間＝2秒
          () {
            // コールバックを渡しておく。ここに遷移メソッドは書いておく
        print('遷移完了');
        Navigator.push(
          context,
          MaterialPageRoute(
            builder: (context) => const MainScreen(),
          ),
        );
      },
    );
  }

  @override
  void dispose() {
    super.dispose();
    //disposeメソッド = タイマーが終わるべき時にタイマーを破棄するメソッド。リソースの解放を担う。
    timer?.cancel();
    //cancelメソッド = disposeなどタイマーが破棄されたりdeactive(日活性状態)になった時に呼び出される。
    //もういいTimerは諦めろ〜って感じでコールバック関数をやめさせる効果を持つ
  }


  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: AppColors.mainWhite,
      body: SafeArea(
        child: Center(
          child: Text('FirstPage',style: TextStyle(fontSize: 35)),
        ),
      ),
    );
  }
}
```
## ポイント
### 1. initState()メソッド中で宣言(定義)する
- **initState()**中で宣言することによってインスタンス作成を初期化時に設定する。
- この画面に来る時に、動作が必ずきっちり画面初期化をする設計の場合スムーズに自動遷移に移れる
- 適当に設計に混ぜていた時に、謎の遷移でこの画面に戻ってきた時に**initState()**が動作せずに固まったことがあるのでそこは注意
### 2. 「Timer」はdart:asyncから持ってくるインスタンス
- `Timer();`でインスタンスを作ることで自動で何らかの処理を負ってくれるタイマー機能を簡単に作れる
- `Timer();`は**第一引数にDuration(遅延時間)**、**第二引数に遅延時間後の処理を記述するコールバック関数**を書いておく。
### 3. dispose()メソッド中には二つメソッドがある
- `dispose`メソッド = メソッドが終わるべき時にメソッドやインスタンスを破棄するメソッド。リソースの解放を担う。
- `cancel`メソッド = disposeなどタイマーが破棄されたりdeactive(日活性状態)になった時に呼び出されるメソッド。実際にタイマーのコールバックメソッドを止めるのはこのcancelメソッド。ぽい。

<br>

以上個人的なメモでした参考になれば幸いです。