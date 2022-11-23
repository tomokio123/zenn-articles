---
title: "【Git】このブランチは 'origin/master' よりも1コミット進んでいます。の解決法のメモ"
emoji: "🕌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Git"]
published: true
---
# 解決法
`$ git pull origin`で解決。

# 原因
#### リモートよりも自分のローカルの方が変更が先行してしまっているということ。

- 現在のブランチがリモートブランチからローカルブランチにフェッチおよびマージされる。また、ローカル追跡ブランチ (origin/mybranch) が最新のコミットを指すように更新される

# リモートのrebaseに追従する方法
`$ git pull --rebase origin develop`
でリベース型のPullができる。
(git fetch + git rebase)

[参考](https://freewayprojects.com/2011/11/solving-the-your-branch-is-ahead-of-originmaster-by-x-commits-issue-after-pulling-using-git/)
