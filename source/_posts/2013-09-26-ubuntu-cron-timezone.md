---
layout: post
title: "タイムゾーンを変更したら cron の実施時刻がずれたので修正した作業メモ"
date: 2013/09/26 00:00
comments: true
categories: 
---

Linux サーバーで cron ジョブを設定したら実施時刻がちょうど9時間ずれていた。初めてのことは何やっても必ずつまづくのぅ。ということで、修正作業メモ。使っているのは `Ubuntu 12.04.2 LTS` 。

## 再現方法

1. `dpkg-reconfigure tzdata` コマンドでタイムゾーンを日本時間に変更
2. `crontab -e` コマンドで cron ジョブを追加
3. cron ジョブ のログを確認すると、実施時刻が指定と異なる <- イマココ！

## 解決方法

cron のサービスを再起動する。

{% codeblock %}
$ service cron restart
cron stop/waiting
cron start/running, process 8575
{% endcodeblock %}

cron のサービスはシステム起動時のタイムゾーン設定を保持し続けるので、サービスを再起動して変更した設定を読み込ませる必要があるみたい。

ちなみに、[参考にしたサイト](http://memo-off.blogspot.jp/2011/11/timezonecron.html) では `service crond restart` コマンドを実行してサービスを再起動していた。Ubuntu は `crond` ではなく `cron` らしい。

## 蛇足

プログラミング関連でつまづいたらまず英語で検索、というのが習慣になっている今日このごろ。上記サイトを参考にコマンド実行するも `crond` なんてないよとエラー。 "ubuntu cron service restart" で出てきた[Linux Start Restart and Stop The Cron or Crond Service](http://www.cyberciti.biz/faq/howto-linux-unix-start-restart-cron/)にある `/etc/init.d/cron restart` を実行したところ、

{% codeblock %}
$ /etc/init.d/cron restart
Rather than invoking init scripts through /etc/init.d, use the service(8)
utility, e.g. service cron restart

Since the script you are attempting to invoke has been converted to an
Upstart job, you may also use the stop(8) and then start(8) utilities,
e.g. stop cron ; start cron. The restart(8) utility is also available.
cron stop/waiting
cron start/running, process 8568
{% endcodeblock %}

なる警告が。「`/etc/init.d` やめて `service` 使ってね」ってことのようだ。Ubuntu 発祥の [upstart - event-based init daemon](http://upstart.ubuntu.com/)が理由みたいなので、時間見つけて違いなど調べてみたい。
