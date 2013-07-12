---
layout: post
title: Amazon EC2を使ってみたら色々とハマったのでメモ
tags:
- life
status: publish
type: post
published: true
meta:
  _edit_last: '2'
---
週に10～15時間程度Windows上で動かしたいツールがあり､年明けから1ヶ月程度､ほぼ毎日Amazon EC2で遊んでました｡だいぶ経験値が溜まったのでここらでひとつまとめておきます｡
<h2></h2>
<h2>Amazon EC2って？</h2>
正式名称<a href="http://aws.amazon.com/jp/ec2/" target="_blank">Amazon Elastic Compute Cloud</a>｡直訳すると｢アマゾン製伸縮自在のオンラインコンピュータ｣ってとこでしょうか｡大別すれば｢レンタルサーバー｣の一種ですね｡公式サイト曰く､特徴は｢弾力性､完全なコントロール､柔軟性､他の Amazon Web Services との統合､信頼性､セキュリティ､安価｣だそうです｡

松竹梅に性能でランク分けされたサーバーを初期費用いくら､月額いくらで契約する従来のレンタルサーバー業者と違い､｢初期費用なし､1時間単位の従量課金､松竹梅のランクは随時変更可能｣なことがウケてここ数年個人･法人ともに急速に利用が広まっています｡個人的にはOSとして使い慣れたWindowsを選べるのがとても魅力｡
<h2>ハマった内容</h2>
<h3>標準で日本語OS</h3>
約3年前の情報(<a href="http://d.hatena.ne.jp/rx7/20090405/p1" target="_blank">Amazon EC2のWindows Serverを日本語化する方法 - RX-7乗りの適当な日々</a>)を頼りに日本語化を試みるもうまくいかず．．．イライラしつつ一度何も仕込まずにWindows Serverを立ち上げたらそのまま日本語OSで起動しました｡EC2にはRegionという｢どの地域でサーバーを立ち上げるか｣を選択出来る機能があり､東京を選ぶことでWindowsの場合は日本語OSが立ち上がるようです｡
<h3>時計ずれまくり</h3>
Amazon EC2をはじめこの類のサービスは｢サーバ仮想化｣という技術によって実現されているのですが､EC2上で仮想化されたWindows Serverは時計がすごく遅れます｡本来の3分の2くらいしか時計が進まない印象です｡スケジューラーなどを利用してツールを走らせる場合これは致命的｡時計合わせのツールはいくつかありますので､適宜設定しましょう｡今回は<a href="http://teps4545.blogspot.com/2009/10/amazon-ec2-windows.html" target="_blank">こちらの方法</a>を参考にWindowsの機能で時計合わせをすることにしました｡ (参考：<a href="https://forums.aws.amazon.com/thread.jspa?threadID=58308" target="_blank">公式フォーラム情報</a>)
<h3>Flashにご用心</h3>
Amazonが用意するWindows Server 2008はどうもAdobe Flashと相性が悪く､<a href="https://forums.aws.amazon.com/thread.jspa?messageID=167931" target="_blank">公式フォーラムによれば</a>随分前から｢ブラウザを巻き込んでFlashが落ちる｣状況が解決されてない様子｡64-bit版のWindows Server2008を選べば回避できますので､Flashを利用する予定の有る方はご注意を｡サーバーOSとはいえWindows7で出来ることは大概出来ます｡
<h3>IPアドレスはオランダにシンガポール？？</h3>
上述のRegionという機能で東京を選択することの最大の利点は【サーバーが物理的に国内にあるのでネットワーク的に近い】ことでしょうが､【IPアドレスが日本のものになる】というのも見逃せません｡位置情報を元にアクセス元を制限しているサービスがよくあるからです｡<a href="https://forums.aws.amazon.com/ann.jspa?annID=1351" target="_blank">公式情報</a>では東京リージョンにはIPアドレスが6ブロック割り当てられていますが､このうち位置情報が【日本】なアドレスは1ブロックだけ！残り5つはオランダ､シンガポール､アメリカなど｡利用の際はご注意を｡
<h2>クラウドをもっと安く -EC2 Spot Instances-</h2>
EC2の利用形態の一つにSpot Instancesというものがあり､うまく利用すると利用料を大きく抑えられます｡<a href="http://aws.amazon.com/jp/solutions/case-studies/foursquare/" target="_blank">Foursquare</a>や<a href="http://aws.amazon.com/jp/solutions/case-studies/so-net/" target="_blank">So-net</a>なども活用しているようです｡基本的な仕組みはEC2の通常利用と変わらないのですが､利用料が｢即決価格ありのオークション方式｣で決まる利用形態です｡具体的には､
<ol>
	<li>利用者は利用料を入札</li>
	<li>Amazonが決める即決価格を上回ればEC2利用可</li>
	<li>即決価格(随時変わる)が入札価格を上回ったらEC2利用強制終了</li>
</ol>
という制約有りの利用形態です｡だいたい4割程度利用料が安くなります(<a href="http://aws.amazon.com/jp/ec2/#pricing" target="_blank">詳細な価格表</a>)｡入札対象となる日時を指定できるので､｢この時間帯だけなるべく安くEC2を使いたい｣という用途にもオススメです｡1月一か月､このSpot Instancesをフル活用､利用料は1,000円以下に収まりました｡

この機能を使えばジュース一本分くらいの費用でそれなりにクラウド体験することができます｡時間を見つけて一度試してみてください｡
