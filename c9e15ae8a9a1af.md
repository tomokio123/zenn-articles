---
title: "[Git]リモートにあるブランチを取ってきてローカルに反映したい時の手順"
emoji: "🐷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Git","Github"]
published: true
---
[参考記事](https://dev.classmethod.jp/articles/how-to-checkout-remote-branch/)

# 起こったこと
- リモートのdefaultブランチがmain(master)ブランチではなく他のブランチ(ここでは**ブランチdevelop**)で、ローカルにはmain(master)ブランチしかない時に、
**リモートのdevelopブランチをpullして新しくローカルにdevブランチを設定したいと思った**。

- ローカルのgitのブランチ確認
```
$ git branch
```
```
*master
```
- masterしか無い。。

- 次にリモートのブランチを確認
```
$ git branch -r 
```
```
origin/master
```
- origin/masterしか無い？？？え？
# 原因
>これはcheckoutコマンドで第二引数に指定した名前（origin/<ブランチ名>）のリモート追跡ブランチが作成（フェッチ）されていないことが原因です。
リモート追跡ブランチの一覧はgit branch -rコマンドで確認可能です。確認してみると現在はdevelopブランチしか作成されていません

## リモートブランチを追跡するのに「リモート追跡ブランチ」を作成（フェッチ）しないといけない！！
- ### リモートブランチからリモート追跡ブランチを作成する
-> `git fetch リモート名(ここではorigin) そのリモートのブランチ名(ここではdevelop) `コマンド！！
```
$ git fetch origin develop 
```
```
 * branch            develop    -> FETCH_HEAD
 * [new branch]      develop    -> origin/develop
```
いけた！

git branchで確認すると
```
* develop
  master
```
かんぺき！


# 手順をまとめると、
1. `git fetch リモート名(ここではorigin) そのリモートのブランチ名(ここではdevelop)`を実行
2. `git branch -r`で確認

***

以上です。間違えが見つかり次第、或いは知識がアップデートされ次第改訂していきます。🙏

初歩的だがdefaultがmainブランチじゃなかったらこうやってローカルにリモートの追跡したいブランチを作成(fetch)すること特になかった。自分で作業用ブランチ(feature)を切ったことはあっても。