---
layout: post
title: "Git とファイルサーバーを使って小規模な分散バージョン管理システムを構築する -Windows 編-"
date: 2013/07/29 06:39
comments: true
categories: git
---

ブログのテーマががっつり偏ってきた気がしますが、自分の興味関心のままに書いているので大目に見てくださいまし。

なお、Mac 編はありませんのであしからず[^01]。

## やりたいこと

1. Windows オンリーなネットワーク環境で
1. インターネットへはアクセスせずに
3. Git とファイルサーバーだけで
1. 分散バージョン管理システムを

簡単に作ってみましょう。

## 想定読者

- ソースコードのバックアップに不満がある
- プログラミングを Windows で行なっている
- 非公開であっても、ソースコードを他者のサーバーやサービスに保存したくない、できない
- Git を使ったことがある、もしくは Git を使ってみる気がある

## 事前準備

<!--more-->

まず、ファイルを読み書きできるファイルサーバーを用意しましょう。今回は Windows なので共有フォルダってやつですね[^02]。

次に、[SourceTree](http://www.sourcetreeapp.com/) をインストール、起動し、初期設定しておきましょう。Git 本体のインストールも行なってくれるので楽ちんです。

詳しい手順は -> [Gitアプリの決定版！SourceTree for windowsがやってきた！ ヤァ！ヤァ！ヤァ！ - damelog][18] を。今回の手順では使用しないので、**「鍵を作る」**の部分は飛ばしてしまいましょう。

## 分散バージョン管理システムを簡単に作ってみる手順

1. パソコン上に Git リポジトリを作る
1. リポジトリ内のファイルをコミットする
1. パソコン上のリポジトリをファイルサーバーにクローンする

大きく分けると以上の3ステップです。順番に説明していきます。

### パソコン上にリポジトリを作る

![](http://lh5.ggpht.com/-IL8-Eo-rmQ8/UfIMvjjkCkI/AAAAAAAABAg/NEPdbgRli0I/s512/ScreenClip%25252520%2525255B0%2525255D.jpeg)  
↑Git でバージョン管理するフォルダを作って[^03]

![](http://lh5.ggpht.com/-yVmkPsly7io/UfIMxJRywTI/AAAAAAAABAw/0eBBwbuSt7w/s512/ScreenClip%25252520%2525255B1%2525255D.jpeg)  
↑SourceTree で Clone/New ボタンを押して出てくるウィンドウからそのフォルダを追加すると

![](http://lh5.ggpht.com/-qB2DpSylaG8/UfIMxNrOEeI/AAAAAAAABAs/Ql0ks-6Z3qE/s512/ScreenClip%25252520%2525255B2%2525255D.jpeg)  
↑Git リポジトリができあがります。SourceTree だとこんな表示

![](http://lh3.ggpht.com/-RAyCDPUtZb0/UfIMyQs7NlI/AAAAAAAABBE/DJwp8qAz-sM/s512/ScreenClip%25252520%2525255B3%2525255D.jpeg)  
↑出来上がった Git リポジトリをエクスプローラーで表示させたところ。 .git というフォルダができていれば OK 。

### リポジトリ内のファイルをコミットする

![](http://lh6.ggpht.com/-yHv4ai9VBpQ/UfIMyWaiBkI/AAAAAAAABBA/vD2l4k-WG7o/s512/ScreenClip%25252520%2525255B4%2525255D.jpeg)  
↑フォルダ内にバージョン管理したいファイルを作って

![](http://lh3.ggpht.com/-Afr4nBJhiH4/UfIMykam5dI/AAAAAAAABBI/0DKEaCQDuRo/s512/ScreenClip%25252520%2525255B5%2525255D.jpeg)  
↑Add ボタンを使ってステージングエリアに追加して

![](http://lh5.ggpht.com/-_MTiFGyC_NA/UfIM0NJauxI/AAAAAAAABBU/YEo9i-xVC9U/s512/ScreenClip%25252520%2525255B6%2525255D.jpeg)  
↑Commit ボタンでコミットっ！！

![](http://lh5.ggpht.com/-FH5-9Nxo8mQ/UfIM0NpOizI/AAAAAAAABBY/4vx0RT76zNs/s512/ScreenClip%25252520%2525255B8%2525255D.jpeg)  
↑変更の履歴が残り、めでたくコミット完了です。

### パソコン上のリポジトリをファイルサーバーにクローンする

![](http://lh4.ggpht.com/-TE_asiXbFX8/UfIQbhXr9TI/AAAAAAAABBw/Z8JUDAX31K4/s512/ScreenClip.jpeg)  
↑SourceTree 画面右上の歯車アイコンをクリック、ターミナルを起動して

{% codeblock %}
$ git clone --bare . //<サーバーの>/<リポジトリにしたい>/<フォルダ>.git
$ git remote add origin //<サーバーの>/<リポジトリにしたい>/<フォルダ>.git
{% endcodeblock %}
↑Git のコマンドを入力していきます。コマンドの内容を説明しておくと

1. パソコン上に作成したリポジトリをサーバー上に複写
2. パソコン上にあるリポジトリの分散先として、サーバー上に複写されたリポジトリを追加

となります。注意点として、まずはフォルダのパス。今回の例だと `C:\easy_dvcs\` ですが、これは `/c/easy_dvcs` と読み替えるようにしてください。日本語含むとうまくいかないこともあるので気をつけましょう。

また、パスの末尾が `.git` となっていますが、これは Git のお作法なのでそのようにしておきましょう。`<フォルダ>.git` という名前のファイルが作られるわけではないのでお間違いなく。

続いて、ちゃんと追加できてるか確認しましょう。

{% codeblock %}
$ git remote show origin
$ git push origin master
{% endcodeblock %}

これらのコマンドを実行して以下のように表示されれば

{% codeblock %}
$ git remote show origin
* remote origin
  Fetch URL: //<サーバーの>/<リポジトリ>/<フォルダ>.git
  Push  URL: //<サーバーの>/<リポジトリ>/<フォルダ>.git
  HEAD branch: master
  Remote branch:
    master new (next fetch will store in remotes/origin)
  Local ref configured for 'git push':
    master pushes to master (up to date)

$ git push origin master
everything up-to-date
{% endcodeblock %}

### 完成！！！

お疲れさまでした。あとは[サルでもわかるGit入門 〜バージョン管理を使いこなそう〜 | どこでもプロジェクト管理バックログ][65]など眺めつつ、プッシュしたりリセットしたりしてみましょう。

## つまづきやすいところ

1. フォルダのアドレスに円マーク(\\)  
文字化けの根源である Windows の円マーク(\\)ですが、 Unix/Linux フレンドリーな Git でもそこが問題になります。SourceTree の[開発元も認識はしている][58]ようですが、Git の問題でもあるので進展はなさそう...  
とりあえず、円マーク(\\)はスラッシュ(/)で置き換えましょう。

1. リポジトリに追加したファイルが、ファイルサーバー上に見当たらない  
Git のリモートリポジトリにはファイルの実体のみが圧縮され保存されています。サーバーのディスクスペースを節約する賢いやり方です。  
コミットし、プッシュしたはずのファイルがファイルサーバー上に見当たらず、「 Git つかえねー」と早とちりしたそこのあなた！`git clone //<サーバーの>/<リポジトリ>/<フォルダ>.git` して確認してみましょう。

## 蛇足

![](http://lh6.ggpht.com/-Pjo7PNj_i88/UfIj9qy9l6I/AAAAAAAABCI/E8Z-ZvzWcM4/capture_intro1_1_1.jpeg)  
↑[サルでもわかるGit入門](http://www.backlog.jp/git-guide/intro/intro1_1.html) つらい、つらいのぅ

データの管理は一切合切こんな状態、その他諸般の事情により「GitHub でソーシャルコーディングヽ(・∀・ )ﾉ ｷｬｯ ｷｬｯ」 などということが夢のまた夢状態...

泣きを見るのは自分なのでソースコードだけは Git でバージョン管理してきましたが、データのありかは開発機のハードディスク。なんとか角を立てずにこのリポジトリを**分散型**に持っていきたいっ！！

ということで調べてみたら思ったより簡単に実現したのでちょっと拍子抜けしておりますが、これでこれからはゆっくり寝られそうです。ありがとう Git!!

## 参考

- [Git - プロトコル][74]  
Pro Git 本でも、Git でリポジトリのリモートとしてファイルサーバーを使用する方法が解説されてます。

- [Using Dropbox to Share Git Repositories](https://gist.github.com/trey/2722927)  
Dropbox を Git リポジトリのリモートとする方法が説明されてます。

[^01]: 同じようにやれば Mac でも問題ない、というかむしろ Mac の方が確実に作業できるはず

[^02]: 個人なら NAS とか外付けハードディスク、法人なら社内向けのファイルサーバーってとこでしょうか

[^03]: この例ではCドライブ直下に `easy_dvcs` というフォルダを作りました

[18]: http://dameleon.hatenablog.com/entry/2013/03/21/185412
[58]: https://jira.atlassian.com/browse/SRCTREEWIN-858
[65]: http://www.backlog.jp/git-guide/
[74]: http://git-scm.com/book/ja/Git-%E3%82%B5%E3%83%BC%E3%83%90%E3%83%BC-%E3%83%97%E3%83%AD%E3%83%88%E3%82%B3%E3%83%AB#Local-プロトコル
