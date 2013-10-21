---
layout: post
title: "NHKラジオのネット配信「らじる★らじる」を録音するツールを Ruby で作った"
date: 2013-10-21 00:00
comments: true
categories: 
---

構想ここ数か月(？)、作業は夜な夜なエンドレス...思い立ったが吉日の結果がようやくまとまった！で勢い余って公開してみた。

名前は ripdiru。勢い余ったことには後悔してないけど、名前は未だしっくり来ず。。ツッコミ大歓迎。

[ripdiru | RubyGems.org | your community gem host][9]

## 何が出来るの？

[らじる★らじる　NHKネットラジオ][39]のライブ配信を MP3 ファイルに録音してくれます。[ラジオ第1][16]、[ラジオ第2][96]、[NHK-FM][48]に対応しています。

番組表と連動しているので、録音を自動停止させたり、番組情報をタグとして MP3 ファイルに埋め込んだりすることもできます。

## インストールと使い方

- Ruby 1.9
- rtmpdump
- ffmpeg

上記3つは不可欠なので入れておきましょう。ffmpeg は曲者なので、[本家からリンクされてるビルド済みファイル][7]を使ってる OS に合わせてダウンロードするのが吉です。

それらのインストールが完了したら、

{% codeblock %}
$ gem install ripdiru
{% endcodeblock %}

を実行すれば ripdiru のインストール完了！あとは

{% codeblock %}
$ ripdiru NHK1
{% endcodeblock %}

などと局を指定して ripdiru を起動してやれば、自動で録音開始→終了。`~/Music/Ripdiru` 配下に MP3 ファイルが保存されているはずです。

### ありがちなエラーと対策

1. libmp3lame がない

続きは後で書きます...(書くよ、うん。)

## 名前の由来

<del>モロパクリ</del>多くのヒントをもらった @[miyagawa][23]さんのツール [ripdiko][63] にちなんだ名前です。らじる★らじるは公式な英語表記が見当たらないのだけど、サイトのソースなどと見てると RADIRU という表記がちらほら。RADIRU を rip するツールということで、ripdiru です。

## 感謝の意

- [miyagawa (Tatsuhiko Miyagawa)](https://github.com/miyagawa/) [ripdiko][63] からのコピペがなければとうてい形にならなかったと思います。感謝。

- [matchy2 (MACHIDA Hideki)](https://github.com/matchy2) [らじる★らじる録音のシェルスクリプト](https://gist.github.com/5310409.git)の rtmpdump まわりの記述を参考にしました。感謝。

- [riocampos](https://github.com/riocampos/) ブログにアップされている[らじる★らじるのあれこれ](http://d.hatena.ne.jp/riocampos+tech/)が参考になりました。感謝。

------------

需要はまったくないでしょうが、プログラミングの近道は「自分がほしい物を作る」らしいので自分にとっては大きな第一歩です。使ってみた！うまくいかない！ここ直して！などなど、ご意見ご感想ありましたら、ぜひ Twitter で @[harupong][52] までお願いします。

[7]: http://ffmpeg.org/download.html
[9]: https://rubygems.org/gems/ripdiru
[16]: http://www.nhk.or.jp/r1/
[23]: https://twitter.com/miyagawa
[39]: http://www3.nhk.or.jp/netradio/
[48]: http://www.nhk.or.jp/fm/
[52]: https://twitter.com/harupong
[63]: https://github.com/miyagawa/ripdiko
[96]: http://www.nhk.or.jp/r2/
