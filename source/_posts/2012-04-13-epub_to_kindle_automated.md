---
layout: post
title: EPUBファイルのKindle転送を自動化したら電子書籍環境が劇的改善した
tags:
- Kindle
- life
status: publish
type: post
published: true
meta:
  _wp_old_slug: epub_to_kindle_auto_part1
  _edit_last: '2'
---
2013/3/13 16:35 追記

[EPUBファイルのMOBI変換とKindle転送を無料で全自動化できなくなったので､無料な半自動化で凌ぐことにした | Pieces of Peace](http://blog.harupong.com/2013/03/epub_to_kindle_automated_part2/)


3013/3/7時点で､変換サービスの提供会社がサービスを有料化しました｡中途半端な対応策を上の記事でまとめてあります｡

以下の手順で全てを自動化するには月5ドルの会費を払う必要がありますのであしからず｡
----------------------

<a href="http://bookstore.g.hatena.ne.jp/worris/20111020/p1" target="_blank">アマゾン・キンドル日本上陸の歴史 - オリノコ川の分水嶺を探査する - bookstore</a>  にもある通り｢Kindle日本上陸！！｣のニュースはすっかりオオカミ少年なわけです｡そんななか昨年<a href="http://blog.harupong.com/2011/10/nice_to_meet_you_kindle/" target="_blank">Kindle4を購入した</a>私は､なんとか日本語を読むべく<a href="http://a2k.aill.org/" target="_blank">青空文庫をKindle</a>に取り込んでみたり､<a href="http://www.instapaper.com/extras" target="_blank">Instapaperを使って日本語WebページをKindleに送ったり</a>してました｡
<h2>ちがうちがう､そうじゃない</h2>
が､なんか違うんですよね｡
<ul>
	<li><strong>何時間読んでも目が疲れないE-Ink！！</strong>
→Instapaperで短いブログ記事ばかり読んでるわ～</li>
	<li><strong>最新作が発売日にすぐダウンロードされる！！
</strong>→青空文庫は古いのばっかりや．．</li>
	<li><strong>Amazonのクラウドと連携していつでもどこでもどのデバイスでも同じ本が読める！！
</strong>→ファイルをUSBで転送してるから､このKindleでしか読めんわぁ</li>
</ul>
と､Amazonと連携して本領を発揮するはずのKindleをまるで活かしきれてなかったんですよね｡まさに豚に真珠､猫に小判｡

そんな具合でふらふらしていたら､｢電子書籍後進国日本における中間解としての有料メルマガ｣などと言う文言をちらほら目にするように｡しかもメルマガの内容をEPUBファイルで配信してる<a href="http://yakan-hiko.com/" target="_blank">夜間飛行</a>というサービスがあると知りさらに興味津々｡｢ファイルフォーマット｣なるありがちな罠に巻き込まれそうになりながらも､なんとかいい具合に｢夜間飛行などで配信されてるEPUBファイルを出来るだけ楽にKindleで読む｣方法を見つけたので紹介します｡
<h2><a href="http://wappwolf.com/dropboxautomator" target="_blank">Wappwolf Automator</a> + <a href="http://www.amazon.com/gp/help/customer/display.html/ref=kinw_myk_pd_ln?ie=UTF8&amp;nodeId=200767340" target="_blank">Kindle Personal Documents Service</a> = Kindleで日本語読み放題！</h2>
<h2></h2>
EPUBファイル（電子書籍用ファイルフォーマットの一種。国内では一番メジャー）をKindleで読むには､現時点で以下のような手順を踏む必要があります｡
<ol>
	<li>EPUBファイルを入手する（サイトからダウンロードする）</li>
	<li>Kindle形式（MOBI形式）にEPUBファイルを変換する</li>
	<li>変換したファイルをKindleに転送する｡</li>
</ol>
うち､自動化が難しい1.（サイトごとの認証がやっかい）以外を自動化してみましょう｡
<h3>用意するもの</h3>
<ul>
	<li>Kindle本体</li>
	<li>EPUBファイル</li>
	<li>Dropboxアカウント</li>
	<li>Kindle Personal Documents Serviceで発行されるメールアドレス</li>
</ul>
上の3つについては説明省きます。最後のは「自分で作ったファイルをKindle用クラウドに置いとく」サービスとでもいいましょうか。詳しくは<a href="http://d.hatena.ne.jp/mteramoto/20111015/1318666503" target="_blank">Kindle Personal Documentに追加された3つの新機能が便利すぎる - mteramotoの日記</a> を。これがないKindleはネットが使えないiPhoneくらい残念感が強くなるので、まだサービスを使ってないKindle所持者はぜひ一度お試しあれ。

上記4点に加え<a href="http://wappwolf.com/dropboxautomator" target="_blank">Wappwolf Automator</a>という自動化ツールを使いますが、このサービスはアカウントがいらないのでここでの説明は割愛。詳しくは以下を。
<h3>設定手順</h3>
↓ 1. Wappwolf Automator (<a href="http://wappwolf.com/dropboxautomator">http://wappwolf.com/dropboxautomator</a>)にアクセスします

<img class="picasa_photo" src="http://lh5.ggpht.com/-CNFG86jH-4w/T4U8WUzupdI/AAAAAAACyXM/ZFeajOInhIc/pic01.png" alt="pic01.png" width="320" height="233" />

↓ 2. Get Started Now -&gt; Login with Dropbox "one folder" accessの順にクリック｡DropboxのIDとパスワードでログイン(初回ログイン時は｢Wappwolf AutomatorにDropboxの情報を参照させていい？｣と聞いてくるのでOKします)

<img class="picasa_photo" src="http://lh5.ggpht.com/-bgrSjVxLwLg/T4U8Yup136I/AAAAAAACyXM/jnw-NlrUCv0/pic02.png" alt="pic02.png" width="320" height="234" /><img class="picasa_photo" src="http://lh5.ggpht.com/-ZuQdiR7T7Cc/T4U8XA7nGhI/AAAAAAACyXM/T_DUsYo_kXQ/pic03.png" alt="pic03.png" width="320" height="233" />

↓ 3. 自動化処理させたいファイルを入れるフォルダ名を指定します(下図の通り､Dropbox/Apps/Wappwolf/ 配下のフォルダ名を指定することになります)

<img class="picasa_photo" src="http://lh4.ggpht.com/-mVsI4pPyQB0/T4U8UzFXfRI/AAAAAAACyXM/gLwTm0WrE5Y/ScreenClip%252520%25255B1%25255D.png" alt="ScreenClip [1].png" width="320" height="232" />

↓ 4. 自動化したい処理を設定する画面になるので

<img class="picasa_photo" src="http://lh6.ggpht.com/-64q_hmm6j3Y/T4U8UzLd1NI/AAAAAAACyXM/jFhqSh3kgCA/ScreenClip%252520%25255B2%25255D.png" alt="ScreenClip [2].png" width="320" height="233" />

↓ Convert eBookを選択し、プルダウンからmobiにして”Add Action"ボタンを押します。

<img class="picasa_photo" src="http://lh6.ggpht.com/-8EPWFbeynt0/T4U8U8X5gOI/AAAAAAACyXM/6OpXSLjfrC8/ScreenClip%252520%25255B3%25255D.png" alt="ScreenClip [3].png" width="320" height="233" />

↓ さらにSend to your kindleを選択､上記「用意するもの」項にあるアドレス等を記入して”Add Action"ボタンを押します。そしたら画面上部のFinished?を押して設定は完了です｡

<img class="picasa_photo" src="http://lh3.ggpht.com/-ROe8vmm5wcw/T4U8V8Da6UI/AAAAAAACyXM/Jrd0i1kAhN0/ScreenClip%252520%25255B4%25255D.png" alt="ScreenClip [4].png" width="305" height="240" />

&nbsp;

これらの手順を踏むことで､｢Dropboxの特定フォルダに保存されたEPUBファイルを自動的にMOBIファイルに変換しKindleに転送する｣という行為を自動化する設定ができあがります。
<h3>利用手順</h3>
<strong><em>用意してあったEPUBファイルを設定手順 3. で指定したフォルダにコピー</em></strong>

以上｡これだけ｡

少し待っていれば､Kindle Personal Document Service経由で指定したKindleにファイルが転送されてきます｡便利すぎる！！

<hr />

流行りだから、話題になってたから、とKindleを買ってみたもののコンテンツがなくて持て余し気味のそこのあなた、これを機会に手元のKindleを活用してみませんか？著名な執筆陣が揃ってる<a href="http://yakan-hiko.com/" target="_blank">夜間飛行</a>なら全メルマガ初月無料で読めますよ～。
