---
layout: post
title: "Re: Git で不要になったリモートブランチを削除する"
comments: true
---

> push で消すのとか、:hoge で指定するのとか、最初ちょっと戸惑った。  
[Git で不要になったリモートブランチを削除する - koogawa blog](http://blog.koogawa.com/entry/2014/03/08/121751)

ローカルの変更をリモートへ反映するのは `git push` でやってね、っていうのが Git を使うときの基本的な考え方だと思っています。なので、Git のコマンドが「リモートブランチを削除」じゃなくて「ローカルブランチの削除をリモートに反映する」なのは納得いくのです。

がしかし、上記引用にもあるように「削除を push で反映」とか「『空』を指定することで削除」とか、直感的じゃなくて何回やっても覚えられないわけです[^01]。

## そんなこともあろうかと！！ -r オプション

`git branch` の[マニュアルページ][1]によると、

> -r  
--remotes  
List or delete (if used with -d) the remote-tracking branches.

ということなので、

```
git branch -d -r <削除したいリモートブランチ>
```

などとして、削除したいリモートブランチを `origin/patch-1` みたいに指定してやればOKです。

**「リモートブランチを消す」**という操作を、`git branch` コマンドを使って直感的に行うことが出来るようになりました。めでたしめでたし。

[1]: https://www.kernel.org/pub/software/scm/git/docs/git-branch.html

[^01]: GitHub でプルリクエストベースで開発してればマージのときに削除用のボタンが、みたいな話はありますが、それはあくまで1ケースなので。
