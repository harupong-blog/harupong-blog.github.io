---
layout: post
title: '「動いているならば、へたに手を触れるな」に決別を｡これからは「直す練習をがしがしやろう」で｡ #jawsdays DAY1参加メモ'
tags:
- life
status: publish
type: post
published: true
meta:
  _edit_last: '2'
---
> **No More "If It Ain't Broke, Don't Fix It". It's all about GameDay!!**

システム運用を根底から覆すような事例に触れた衝撃を表すいい表現を思いつけたし､色々といい気づきをいただきました｡参加できて良かったです｡

素晴らしい機会を提供してくださったJAWSUGのみなさん､本当にありがとうございました｡

ということで､以下､参加メモ｡

## [プレゼンテーション]Behind the scenes of Presidential Campaign 米大統領選挙の舞台裏

- 2012年米大統領選挙､オバマ陣営を支えるITシステムの構築･運用の裏側｡アプリケーション数200､仮想サーバ2000台、毎秒1万のリクエスト、180TBのデータウェアハウスに対してのべ85億回ものリクエストを処理した｡
- 政府が提供する選挙人名簿とSNSへの書き込みを分析､地元周りするボランティアのスマートフォンに｢誰がまだ投票してないか｣をリアルタイムで知らせるアプリや､広告の費用対効果を最大化するアプリなどが運用された｡後者のアプリは広告費を2億ドルほど削減した｡
- バックアップサイトをアメリカ西海岸に構築｡500程度の仮想サーバ､27TBのデータを9時間程度で移行することができた｡
- 本気で｢システム復旧ゲーム｣をやったのはいいアイディアだった｡チームを壊す人と直す人に分ける｡壊す人はいやらしい方法で壊す｡直す人はその手順をドキュメントやスクリプトに残しておく｡直す手順が残るし､何より直したことがある､というのがエンジニアの自信に繋がる｡そういったゲームをやるにもクラウド技術は最適だった｡

## [パネルディスカッション]クラウドファースト時代に求められるシステムインテグレーター像とは？

- 自前で作ることを良しとせず､自社ソフトウェアのハードウェアウェアリース切れに伴う延命､パッケージソフトウェアを運用するためのインフラとしてAWSを選定される顧客が多い｡
- AWSを利用する日本企業は業種を問わず､｢自社の本業に専念するために､サーバーの運用といった共通部分は極力プロに任せたい｣という意向が強い｡
- IT関連のパラダイムが変わってきている。違うゲームに突入していくような感じを受ける。昔だったらCOBOL、データベースなどテクノロジーカットで専門家になれれば食べていくことができた。しかし、現在は辛くなってきている。
- テクノロジーカットの専門家ではなく、横串でクラウド基盤全般に強いとかそういう組み合わせの知識が必要になってくる。使う技術に長けている人が重宝されるだろう。

## [パネルディスカッション]クラウド時代の個人に必要な変革とは?

- クラウド技術､特にAWSによってサーバー運用がAPIで可能になりプログラムできるようになったため､開発者(Developer)と運用者(Operator)の一人二役(DevOps)が強く求められている｡
- Devでの手抜き､稚拙さがOpsでの負担となって自分に返ってくるので､Devの品質をあげる良いモチベーションになっている｡
- 一人で何もかも処理しないといけない分､末端のノウハウや知識でなく､古典にあたって根っこの部分の知識を習得するのが大事｡

---------------
ブログに書くまでがイベント参加だ､と偉い人が仰ったとかなんとか｡ようやっと自分の中のJAWSDAYSを終えることができました｡ツッコミなどありましたら[Twitter](https://twitter.com/harupong)の方までぜひよろしくお願いします｡

### 併せて読みたい

- スピーカーの紹介とセッションの録画
	- [プログラム・スピーカー紹介 | JAWS DAYS 2013 | 2013/3/15（金）～16日（土）東京ビッグサイトで開催！](http://jaws-ug.jp/jawsdays2013/speaker.html)
	- [USTREAM: JAWS DAYS 2013: JAWS-UGのアニュアルイベント JAWS DAYS 2013 メインチャンネルです。](http://www.ustream.tv/channel/jawsdays)
- Miles Ward氏によるプレゼンテーション "Behind the scenes of Presidential Campaign"関連
	- [「Obama For America」の開発チームが作り上げた大規模な選挙キャンペーンシステムの舞台裏（前編） － Publickey](http://www.publickey1.jp/blog/13/obama_for_america.html)  
	- [「Obama For America」の開発チームが作り上げた大規模な選挙キャンペーンシステムの舞台裏（後編） － Publickey](http://www.publickey1.jp/blog/13/obama_for_america_1.html)
	- [オバマ選挙戦から学ぶ！スタートアップに使えるメソッド「GAME DAY」【JAWS DAYS 2013】 | WooFla!](http://ooze-flash.com/2013/03/jaws-days-for-startup.html)
	- [Obama for America Case Study: Amazon Web Services](http://aws.amazon.com/solutions/case-studies/obama/)
	- [Amazon.co.jp： Learning from First Responders: When Your Systems Have to Work eBook: Dylan Richard: Kindleストア](http://www.amazon.co.jp/Learning-First-Responders-Systems-ebook/dp/B00BJR3MOO/)
