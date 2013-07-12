---
layout: post
title: SONYのレシーバーで苦々しいBluetoothデビュー
tags:
- audio
- bluetooth
- gadget
- life
status: publish
type: post
published: true
meta:
  _edit_last: '2'
  _sd_is_markdown: '1'
  _wp_old_slug: first-bluetooth-device
---
<p>iPhone用に使っているイヤフォンが断線してしまったので代替品を探していたらこんな記事を見つけました｡</p>

<p><a href="http://blog.sorah.jp/2012/04/26/drc-bt30">SONY の Bluetooth ヘッドセットが便利 - sorah.blog</a>
レジーバーだけで8000円は高い！となったものの気になって調べてると､<strong>イヤフォン抱き合わせのセット商品</strong>としてメーカーからリニューアル発売されてました｡しかも格安5000円！売れ残り処分を疑いたくなる価格設定ですが､評判はよくないしBluetooth使ってみたかったのでポチッと買ってみました｡</p>

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:81867AAF-BB02-476b-AE5D-12BDAC2E750D:26a594b7-8b86-4f9d-90b9-20f930ba9c26" class="wlWriterEditableSmartContent">
  <a href="http://www.amazon.co.jp/exec/obidos/ASIN/B002P67DZW/harupong-22/ref=nosim" target="_blank"><img alt="SONY Bluetooth ワイヤレスオーディオレシーバー BT30 ブラック DRC-BT30/B" src="http://ecx.images-amazon.com/images/I/31a14WHtxkL._SL160_.jpg" /><br />SONY Bluetooth ワイヤレスオーディオレシーバー BT30 ブラック DRC-BT30/B<br /></a>
</div>

<p>↑2009年に発売されたレシーバー単体モデル｡</p>

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:81867AAF-BB02-476b-AE5D-12BDAC2E750D:be1ebba8-bd59-472d-be4f-f19586ac6ee5" class="wlWriterEditableSmartContent">
  <a href="http://www.amazon.co.jp/exec/obidos/ASIN/B005LA53FG/harupong-22/ref=nosim" target="_blank"><img alt="SONY ワイヤレスステレオヘッドセット BT63EX ブラック DR-BT63EX/B" src="http://ecx.images-amazon.com/images/I/41CwozhgbQL._SL160_.jpg" /><br />SONY ワイヤレスステレオヘッドセット BT63EX ブラック DR-BT63EX/B<br /></a>
</div>

<p>↑2011年にリニューアル発売されたイヤフォン(MDR-EX60SP)同梱モデル｡</p>

<h2>ハマった点1：工場出荷時は｢低音質設定｣</h2>

<p>Bluetoothにはファイル転送用やマウス･キーボード接続用など様々な<a href="http://ja.wikipedia.org/wiki/Bluetooth#.E3.83.97.E3.83.AD.E3.83.95.E3.82.A1.E3.82.A4.E3.83.AB">接続方式(プロファイル)</a>があります｡音声転送系は通話用のHSP/HFPと音楽再生用のA2DP ｡前者はマイクが使えるけど低音質､後者はマイク使えないけど高音質です｡Bluetoothレシーバー部分にマイクを内蔵している本製品では､両方のプロファイルを用いて｢高音質に音楽配信しつつ､マイクも使える｣という使い方ができます｡これは便利｡</p>

<p>しかも､SONYの製品ページでは本製品を<a href="http://www.sony.jp/headphone/products/DR-BT63EX_DR-BT63EXP/">｢iPhoneやスマートフォンなどの音楽をワイヤレスでリスニング｣</a>と紹介しており､当然のごとく上記のような｢両プロファイル同時接続設定｣が標準でなされてると思いきや､なんと工場出荷時は音声転送系のプロファイルのみを使用するような設定になっています｡しかもマニュアルにも｢ち～さく｣しか書かれておらず､これはなかなか気付けない｡ひどい．．</p>

<p>設定方法は以下の通り｡これから購入される方は開封後すぐ､既にお持ちの方も一度確認してみるといいです｡音質劇的に変わりますよ｡</p>

<ol>
<li>電源を切る｡</li>
<li>ジョグボタン(停止/再生ボタン)と電源ボタンを7秒間長押し｡</li>
<li>電源ランプが点滅→1度だと両プロファイル利用､2度だと通話用プロファイル利用設定｡</li>
</ol>

<h2>ハマった点2：Windows7を巡るメーカーの案内がひどい</h2>

<p><a href="http://qa.support.sony.jp/solution/S1110278034013/?p=&amp;q=Windows7&amp;rt=qasearch&amp;srcpg=ce">Windows 7に対応していますか。 | Q&amp;Aページ | Q&amp;A | ヘッドホン／AV関連製品/アクセサリー | サポート・お問い合わせ | ソニー</a>
ソニーのFAQページにWindows7に関する項があり､｢対応しております｣と書かれてるので｢さすが！｣とぬか喜びしたわけですが､よく読んでみると｢USB充電について対応しております｣の記載｡</p>

<p><strong>なんだよそれ！！</strong></p>

<p>要はWindows7なパソコンのUSBポートに繋ぐと充電できますよ､と｡そんなこと誰も聞いてないですソニーさん．．．</p>

<p>ということで､公式にはWindows7対応のドライバがないこの製品｡型番で検索かけると皆さんが奮闘されてる様子が伺えます｡ノートパソコンのメーカーHPなどでドライバを探してみるとよさそうですね｡手元のパソコンでは検索でひっかかったLenovoのドライバが今のところうまく動いてるようです｡</p>

<hr />

<p>とは言え､無線でクリアな音声が飛ばせ､マイクも内蔵で会話ができ､しかも好みのヘッドフォンが接続可能となかなか優秀な製品だと思います｡電池交換出来なさそうなので､予備にもう一個買っておきたいくらい｡しばらくお世話になりそうです｡</p>
