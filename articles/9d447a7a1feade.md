---
title: "【Flutter】画面上部のTabBarの作り方"
emoji: "📌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Flutter","Dart","TabBar","NavigationBar"]
published: true
---
[TabBarClass公式](https://api.flutter.dev/flutter/material/TabBar-class.html)
# 画面上部のTabBarの作り方について
## 1 TabBarウィジェットを設置する(AppBarのtitle内)
```dart
appBar: AppBar(
    title: TabBar(//①TabBarWidgetで上部のバーが作れる
        onTap: _onItemTapped, //タップしたtabs:[] のindex番号を_onItemTappedの引数(int index)として渡している
        tabs: [
          Tab(text: '1',),
          Tab(text: '2'),
        ],
        indicatorColor: Colors.white,
    ),
),
```

重要なプロパティ三点↓
- `onTap：`：Tapした時の挙動
- `tabs:`：実際のバー
- `indicatorColor：`：バー下にある細い線(indicator)の色

## 2 色々必要物を定義する
```dart
int _currentPageIndex = 0;//ここにTapされたバーのindex番号を入れる

  void _onItemTapped(int index) {
    //Tapされたindexを上記_currentPageIndexに代入するメソッド
    setState(() {
      _currentPageIndex = index;
    });
  }
  static const List<Widget> _widgetOptions = <Widget>[
    //Widgetの配列を持つ要素を定義しておく。_currentPageIndexの値に応じたWidgetはここに用意。
    Page1(),
    Page2()
  ];
```
- `_currentPageIndex`：適用するバーのindex番号を格納する変数。


- `_onItemTapped(int index)`：適用するバーのindex番号を`_currentPageIndex`に格納するメソッド。この中の`setState`メソッドが回ることによって再描画され、タップされたバーのindex番号が`_currentPageIndex`に入る


- `_widgetOptions`：index番号に対応する画面(Widget)のリスト。

## 3 出し分ける画面のWidgetをchildの部分に設置する
```dart
body: Column(
  children: [
    Expanded(
      child: Container(
        child: _widgetOptions.elementAt(_currentPageIndex)))
        //elementAt()メソッド：()内のindex番号を取り出す。ここでは　_currentPageIndex　を渡す
  ],
),
```
- 2で定義したWidgetを駆使してchildなどに渡す

# サンプルコード

```dart:main.dart
class MainPage extends StatefulWidget {
  const MainPage ({Key? key}) : super(key: key);

  @override
  State<MainPage> createState() => _MainPageState();
}

class _MainPageState extends State<MainPagePage> {

  int _currentPageIndex = 0;// ②：ここにTapされたバーのindex番号を入れる
  void _onItemTapped(int index) {
    //Tapされたindexを上記_currentPageIndexに代入するメソッド
    setState(() {
      _currentPageIndex = index;
    });
  }
  static const List<Widget> _widgetOptions = <Widget>[
    //_widgetOptionsというWidgetの配列を持つ要素を定義しておく
    Page1(),
    Page2()
  ];

  @override
  Widget build(BuildContext context) {
    return DefaultTabController(
      length: 2,
      child: Scaffold(
        appBar: AppBar(automaticallyImplyLeading: false,
            // //以上の記述一行だけでNavigationのBack矢印が消せる。豆知識
            title: GestureDetector(//このGesutureDetectorは要らないです今気づきました。


              child: TabBar(//①TabBarWidgetで上部のバーが作れる
                onTap: _onItemTapped, //タップしたtabs:[] のindex番号を_onItemTappedの引数(int index)として渡している
                tabs: [
                  Tab(text: '1',),
                  Tab(text: '2'),
              ],
              indicatorColor: Colors.white,
        ),
            ),
        backgroundColor: Colors.blue),
        body: Column(
          children: [
            Expanded(
                child: Container(
                    child: _widgetOptions.elementAt(_currentPageIndex))) 
                    //③タップしたバーによって表示を変える。そのwidgetをここに設置している
                     //elementAt()メソッド：()内のindex番号を取り出す。ここでは　_currentPageIndex　を渡す
          ],
        ),
      ),
    );
  }
}
```

![](https://github.com/tomokio123/zenn-articles/blob/main/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202022-12-16%2011.54.12.png?raw=true =400x400)