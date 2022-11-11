---
title: "【Flutter】ホットリロードとは何なんですか"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Flutter","Dart"]
published: true
---
ホットリロードてなんやと思ったので調べてみた。公式を大いに引用。
[公式リファレンス・ホットリロード](https://docs.flutter.dev/development/tools/hot-reload)
[AndroidStudio IconReference](https://www.jetbrains.com/help/idea/symbols.html)
# ホットリロードとは
>Flutter’s hot reload feature helps you quickly and easily experiment, build UIs, add features, and fix bugs. Hot reload works by injecting updated source code files into the running Dart Virtual Machine (VM). After the VM updates classes with the new versions of fields and functions, the Flutter framework automatically rebuilds the widget tree, allowing you to quickly view the effects of your changes.

つまり

「**更新されたコード実行中のDart仮想マシン(VM)に挿入し、VMがウィジェット ツリーを自動的に再構築し、画面を更新する**」

こと

# ホットリロード、ホットリスタート、フルリスタートの違い

- **ホット リロード**(hot reload)：Widgetの再構築を行う→**buildメソッドを実行するのみで状態を維持しつつ**`main()`**メソッドや**`initState()`**メソッドは実行されない**


- **ホット リスタート**(hot restart)：Flutterアプリの**状態を失い**(Dart部分)、再起動する。`main()`**メソッドや**`initState()`**メソッドも再実行**される。
(`flutter run`とやっていることはあまり変わらないのかな？と思う。`flutter run` の方がやる範囲は少し大きそうだけど。ここはまだ認識甘い)


- **フル リスタート**(full restart)：**完全に再起動(stop → run)**する。ので**iOS、Android、Webアプリを再起動する**と言うこと。つまりFlutter部分以外の**ネイティブコード（Java/ Kotlin/ObjC/Swift）も再コンパイルする**ので比較的時間を要する。

![](https://docs.flutter.dev/assets/images/docs/development/tools/android-studio-run-controls.png)

# どういう時にどんなリスタートをするべきか

→ **ホットリロード > ホットリスタート > フルリスタート**の順で「軽めの再構築」らしいね。以下ではどういう時にどんなリスタートをするべきか考える
サンプルコードは一番下に置いとく。

## 1. ネイティブコードが変更された時は
- ネイティブコード(Kotlin、Java、Swift、Objective-Cなど)を変更した場合は、**フルリスタート・完全な再起動**(アプリを停止(stop)して再起動(run)する)を実行して、**変更が有効になることを確認する必要**がある。


上記のフルリスタートの仕組みによる。ホットリロード、ホットリスタートではそこまでの変更を見ない。特に外部APIを使用するときにXcode(の権限系とか？)を編集するとネイティブコードが書き変わったりする。そこで正常なはずなのに例外などが出た場合は、フルリスタートすると治ることが多い.(AndroidStudioの赤四角でApp stopして →  Debug run/run)


## 2. カウンターの値をもとに戻したい時は
- カウンターの値をもとに戻したい場合は、**ホットリスタート**(flutterアプリ部分を再起動し、状態も一度破棄)を実行して、**状態**(ここでは`_counter`)**をリセット**する。(hot restartボタン押す)
`main()`**関数や**`initState()`**からやり直す**のでその中にある`_counter`変数も一回破棄されるわけだ

- AndroidStudio の hot restart を押すと以下

![](https://github.com/tomokio123/zenn-articles/blob/main/Simulator%20Screen%20Shot%20-%20iPhone%2012%20Pro%20-%202022-10-10%20at%2012.23.12.png?raw=true =210x42)

状態が変わった。


## 3. カウンターの値を戻さず描画の確認をしたい時は
- カウンターの値を戻さず描画の確認をしたい時は場合は、**ホットリロード**(buildメソッドを実行するだけなので状態は保持)を実行して、`_counter`の**状態を保持しつつ、再描画**する。(hot relord おす or command + sで保存)

- AndroidStudio で command + s を押すと以下

![](https://github.com/tomokio123/zenn-articles/blob/main/Simulator%20Screen%20Shot%20-%20iPhone%2012%20Pro%20-%202022-10-10%20at%2012.23.43.png?raw=true =210x42)

状態は変わらんかった。

保存時にホットリスタートできるから状態を維持しつつ画面がどんどん円滑に再描画できるのでコスパが良く、UIの確認も超楽なのがいい点よね


### Sample Code 
```dart:main.dart
import 'package:flutter/material.dart';

class DefaultCodePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key? key, required this.title}) : super(key: key);
  final String title;
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.bodyLarge,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}
```


