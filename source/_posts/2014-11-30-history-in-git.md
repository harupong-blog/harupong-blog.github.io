---
layout: post
title: "Git における「歴史」と「履歴」"
comments: true
---

[Pro Git の翻訳](https://github.com/progit/progit2-ja)をしていたら、

1. `history` → 歴史
1. `commit history` → コミット履歴[^01]

訳しわけられていることに気付いた。履歴で統一してもよさそうなのに……と思っていたら、経緯を教えてもらえたのでメモ。ちなみに広辞苑によると、

> **り‐れき【履歴】**  
> 現在までに経て来た学業・職業などの次第。来歴。経歴。
>
> **れき‐し【歴史】**  
> 1) 人類社会の過去における変遷・興亡のありさま。また、その記録。「―に名を残す」「―上の人物」  
> 2) 物事の現在に至る来歴。「―と伝統を誇る」  

だそうです。

## 書き換えられるのは歴史、そうでないのが履歴

結論を先に書いておくと、

1. **“Because he said so.”** 現メンテナーがそう呼んでいるから
2. バージョン管理の文脈では「履歴」が一般的だから

の2点が使い分けの理由。後者は違和感がないのだけど、前者は「んんっ！？」と思ってしまうところ。

で、さいわいなことに、Git の現メンテナーである濱野さんがこの件に関して自分の考えを述べられている文章があった。

> One thing that seems to have surprised the audience was that I used the word "history" a lot.   
[gitster's journal - git talk in Japan](http://gitster.livejournal.com/16753.html)

2008年に濱野さんを招いた Git の勉強会が開催されたのだけど、「自分が history という単語を何度も使ったことにみんな驚いていたっぽい」とのこと。その勉強会の模様については以下を参照。

- [ねこげっとぷれす » git勉強会（動画）](http://pneskin2.nekoget.com/press/?p=146)
- [git勉強会動画の見どころ - m-takagiの日記](http://d.hatena.ne.jp/takagimasahiro/20081008/1223413046)

で、この一件を踏まえた濱野さんの考察がさきほどの文章にあり、

> Especially because the latter word in Japanese 履歴 has strong connotation of looking back what happened in the past and because the stress is on recording whatever random thing you did in a single strand of pearls (as opposed to the process of thinking about and building what is to be recorded), it does not mesh well with the way git freely lets you rewrite histories -- the way we look at histories in git has stronger stress on the process of building and reshaping histories suitable for our purpose.

> （意訳）履歴、というと、過去に起こったことを一連の記録として残しておくニュアンスが強いけど、用途に応じて history を自由に書き換えられる Git の仕組みにはそのニュアンスは馴染まないよね。

とおっしゃられている。確かに、リベースしたり `push -f` しちゃえばいくらでも歴史は書き換えられるし、意図してそのように作られたソフトウェアなのだから、「歴史」という言葉のニュアンスのほうがよく合うのかもしれない。

Git のダメなところとして「history が自由に書き換えられるところ」を挙げる人がいらっしゃるようだけど、それが **Git way** だということ。のちに振り返る人にわかりやすくなるように、積極的に歴史を書き換えていこうと思った次第。

[^01]: 「コミットの歴史」と訳されてる箇所もあるのだけど、おそらくこれは翻訳作業の齟齬だと思う。
