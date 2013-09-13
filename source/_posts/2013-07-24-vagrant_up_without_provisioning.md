---
layout: post
title: "Vagrant でプロビジョニングを実行せずに VM を立ち上げる方法"
date: 2013/07/24 06:28
comments: true
categories: blog
---

## 2013/09/13 追記

2013/09/05 にリリースされた 1.3.0 から、

> `vagrant up` will now only run provisioning by default the first time it is run.

ということで、以下の workaround 的な方法は必要なくなったみたい。やったね！！

詳しい日本語解説記事↓。

[Vagrant 1.3.0 からは2回目以降の起動時にプロビジョニングが自動で走らないので注意 - 頭ん中](http://www.msng.info/archives/2013/09/vagrant-1-3-0-no-provision.php)

---------------------

{% codeblock %}
$ Vagrant up --no-provision
{% endcodeblock %}

と、`--no-provision` を付けて VM をたちあげてやればいい。

## 背景

[Pro Git 日本語版電子書籍公開サイト][20]で配布しているデータを生成するための環境作りとそのメンテナンスをなるべく手抜きするために色々と試行錯誤している。

EC2 やら試してみたけど、今のところ **[Vagrant][71] でローカル環境上に Ubuntu を Pro Git 仕様にプロビジョニングする**、というのが一番しっくり来てるところ[^01]。

で、Vagrant といえば `Vagrant up` なのだけど、Vagrantfile にプロビジョニング設定を書いた状態で `Vagrant up` すると必ずプロビジョニングが実行されてしまう。

初めて VM を立ち上げる時はそれでも構わないのだけど、プロビジョニング済み VM を一度シャットダウンの、その後立ち上げるときにまたプロビジョニングが行われてしょんぼりしていた。

どちらかと言うと `--provision` オプションをつけて VM を立ち上げたときだけプロビジョニングを実行する、みたいにして欲しいのだけど..[^02]

[20]: http://progit-ja.github.io/
[71]: http://www.vagrantup.com/

[^01]: どうやってるかは別途まとめる予定

[^02]: そのようにプロビジョニングを作りこめばいいじゃん、というツッコミはスルーします
