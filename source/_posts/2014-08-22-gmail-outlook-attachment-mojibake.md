---
layout: post
title: "Gmailの添付ファイル文字化けとその対策"
comments: true
---

## TL;DR 先に結論

Gmailで添付したテキストファイルが受信側で文字化けしてしまう問題は、**テキストファイルの文字コードをUTF-8にする**ことで回避できます(中の人も問題は認識している模様)。

## 詳細

<blockquote class="twitter-tweet" lang="en"><p>Gmailでテキスト文書を添付して送ると相手方で文字化けする例が明らかに増えてるんだけど、これはどうにかならないものかね。相手がOutlookの場合が多いようだけど、きのうの相手はShurikenだという。いい対策はないかな。</p>&mdash; 越前敏弥 Toshiya Echizen (@t_echizen) <a href="https://twitter.com/t_echizen/statuses/501887964620656640">August 20, 2014</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

ミステリー翻訳家として第一線で活躍されている[越前敏弥 - Wikipedia][30]さんのこんなツイートが流れてきて、「へー、未だにそんなことあるんだ。メーラーのせいじゃないの？」と思いつつも気になってググってみたら、

[添付ファイルの文字コード「shift-jis」をGmailが「us-ascii」と誤判定して送信するため、これをOutlookで受信すると文字化けが発生します。 - Google プロダクト フォーラム][81]

というスレッドが2014年2月にたてられていて、Googleの中の人から「問題は認識しているが修正時期は未定」というコメントもついていました。スレッドによると

> - Gmailの添付ファイル送信→Outlookの添付受信で現象が発生。
- Gmailで添付送信するファイルの文字コードは「shift-jis」
- 日本の文字化けが確認されたファイル形式（拡張子）は「.txt」、「.csv」、「.html（または.htm）」
- 日本語全角450文字（900バイト）あたりでGmailの添付ファイルに対する文字コードが変わってしまう。

どうも、日本語の文字数が一定量を超えると添付テキストファイルの文字コードをUS-ASCIIとして処理してしまうようです。手元で試したところ、添付ファイルの文字コードが「EUC-JP」の場合でも同じように誤処理されてしまいました。

上記のツイートのとおり、Gmail<->Outlookの組み合わせ以外でも起こるようなので、割と影響範囲大きいかも。

[30]: http://ja.wikipedia.org/wiki/%E8%B6%8A%E5%89%8D%E6%95%8F%E5%BC%A5
[81]: https://productforums.google.com/forum/#!topic/gmail-ja/R4Qpo9kt6a8
