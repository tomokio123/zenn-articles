---
title: "[Flutter]StateProviderの使い方と個人的まとめ"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Flutter","Dart"]
published: true
---
[参考資料(公式)](https://riverpod.dev/ja/docs/providers/state_provider/)
# StateProviderとは
- StateProvider は**シンプルな(簡単な)値を管理するためprovider**。 `StateNotifierProvider` の簡易版で、ステートの管理にわざわざ StateNotifier クラスを定義するほどではない場合に使う。
(シンプルな値：int や String などを指す。例は以下)

#### 要は
- 比較的シンプルな値を扱うときは`StateProvider`で、クラスやその他だるいロジックがある時は`StateNotifierProvider`でプロバイダーを作る
>StateProvider は UI 側で利用される シンプルなステート を管理するのにうってつけでしょう。 シンプルなステートとは、次のような型のステートのことを指します。
>- 列挙型（enum）、例えばフィルタの種類など
>- 文字列型、例えばテキストフィールドの入力内容など
>- bool 型、例えばチェックボックスの値など
>- 数値型、例えばページネーションのページ数やフォームの年齢など

>逆に言えば、StateProvider は次のようなステートを公開するために使うべきではありません。
>- ステートの算出に何かしらのバリデーション（検証）ロジックが必要
>- ステート自体が複雑なオブジェクトである（カスタムのクラスや List/Map など）
>- ステートを変更するためのロジックが単純な count++ よりは高度である必要がある

# 使い所とsample code(とPoint)
### 使い所
- ドロップダウンメニュー(押したら下にニューって伸びるメニュー)
- テキストフィールド(入力できる欄)
- チェックボックス(チェックマーク入れるアレ)
など。

### sample code(使うPoint)
[参考資料](https://qiita.com/yukihiroK/items/40d927b46106f06b2f65)

```dart:main.dart
final countProvider = StateProvider<int>((ref) => 0);
//②：グローバルに「countProvider」という名のProviderを設置した！

class CountSample extends ConsumerWidget {
  //①：ConsumerWidgetでラップし、状態を追えるようにする
  @override
  Widget build(BuildContext context, ScopedReader watch) {
    final count = watch(countProvider);
    //③：ここで更新された値が変数countに格納されている。その格納のためのwatch。

    return Scaffold(
      appBar: AppBar(
        title: Text('Count Sample'),
      ),
      body: Center(
        child: Text('Tapped ${count.state} times.'),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          count.state++; 
          //count 変数内のstateに変更を加える。
          //するとプロバイダ(②)の状態を変更することになり、それを③で検知する流れになる
        },
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}
```

#### サンプルコードを見るときのPoint
1. `ConsumerWidget`でラップされるとproviderの状態の変化( ref というオブジェクトの値)を追える。
(公式さんによるとウィジェットツリーの中で ref を取得する(状態を追う)には **StatelessWidget を ConsumerWidget に置き換える**のが基本らしい。)

2. Provider(今回は簡単な値なので`StateProvider`)を使って、**グローバルにプロバイダを設置**。
(sample codeの１行目、`final countProvider = StateProvider<int>((ref) => 0);`の部分！)

3. `ref.watch`: プロバイダの値を取得した上で、その**変化を監視**する。**値が変化すると、その値に依存するウィジェットやプロバイダの更新が行われる**。
(`ref.watch`の時点で状態を更新する。sample codeではその更新された値は「count」`final count = watch(countProvider);`という変数に入ってる)

#### ※公式さんより一言
>NOTE
機能の実装時には可能な限り ref.watch を使用するようにしてください。 ref.watch によりアプリはリアクティブかつ宣言的になり、コードの保守性を高めることができます。

# ProviderとStateProviderの違い
- `Provider`:読み取り専用。後から値を変更できない(状態変更できない)ことによる。
- `StateProvider`:状態変更用。後から値を変更できる(状態変更できる)ことによる。
#### 普通はProviderは状態管理の仕組みやから「Provider」では作らず、StateProviderやStateNotifierProviderでプロバイダを設置するのが普通と思う。


<br>
***

以上です。間違えが見つかり次第、或いは知識がアップデートされ次第改訂していきます。🙏
中国語でもちょっとずつ書いてみました。謝謝你的瀏覽


