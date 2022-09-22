---
title: "[flutter]要素を親要素に１行で収めたいとき(FittedBox)"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Flutter","Dart"]
published: true
---
# 問題
- ページタイトルが長くはみ出してまう。。。とき
# 対処法
- **FittedBox** ウイジェットを使おう
```dart
FittedBox(
  fit: Boxfit.fitWidth,
  child: Container(//子要素
  )
)
```
的なかんじ

```dart:main.dart
appBar: AppBar(
        title: FittedBox(
          fit: BoxFit.fitWidth,
            child: const Text("ルートびっひバンベートーベンなドラえもんNNNNNN!!")),
      ),
```
- 結果は「ルートびっひバンベートーベン,,,」的なやつが
- 「ルートびっひバンベートーベンなドラえもんNNNNNN!!」って１行で表示されAppBarにすっぽり！！
(画像貼るの面倒っす)

### もちろん後者の方が文字はちっちゃくなるけど！