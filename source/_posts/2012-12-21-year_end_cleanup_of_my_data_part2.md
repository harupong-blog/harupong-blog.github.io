---
layout: post
title: 年末データ大掃除(2) ～Amazon S3/Glacierに全てを漏れなく安価に保管できてすっきりした編～
tags:
- backup
- glacier
- life
- photo
- Picasa
- picasa web album
- s3
status: publish
type: post
published: true
meta:
  _edit_last: '2'
---
[前編はこちら｡][01]今回は後編と考察です｡

## 大掃除その1. Googleからデータを削除した

容量130GB､ファイル数にして4万弱の画像､映像ファイル｡漏れなく[Picasa Web Album][02]にアップしていましたが､まずはこれを全て削除しました｡ただし[Picasa Web Album][02]のネット経由での閲覧機能は便利に使っていたので､[Google+に参加して高解像度画像を無料で保存](http://support.google.com/picasa/bin/answer.py?hl=ja&answer=1224181)できるようにし､ネット経由での閲覧手段も併せて確保しました｡

![Google Storage](http://lh6.googleusercontent.com/-6gIx76Pz8rI/UMa5AZaCj7I/AAAAAAAAAbI/gfoc_EORqcs/s471/googlestorage.jpeg?gl=JP)  
↑無料会員であることを示す1024MBの文字｡しかも使用量0MB(0%)｡すっきりです｡

## 大掃除その2. オンラインバックアップサービスに全てを漏れなく保管した

[計画を立てている段階][01]では､Googleに代わるバックアップ先として[無制限定額なCrashplan](http://crashplan.com)を使う予定でしたが､実際に使ってみてアップロード速度の遅さに辟易[^02]｡[その後Amazonのストレージサービスをあれこれ試し](http://blog.harupong.com/2012/12/online_backup_payperuse_vs_unlimited/)､最終的に[**Amazon S3にアップロードしてAmazon Glacierに保管する方式**][03]に落ち着きました｡

[![S3 Console](http://lh5.googleusercontent.com/-MN0n2A_5A50/UNPPXRmsqoI/AAAAAAAAAc4/RU2bS3CzBsU/s512/S3Console.jpeg)](http://lh5.googleusercontent.com/-MN0n2A_5A50/UNPPXRmsqoI/AAAAAAAAAc4/RU2bS3CzBsU/s1000/S3Console.jpeg)  
↑S3のコンソールで見るバックアップデータのリスト｡プライスレスな4万弱､130GBの写真や動画がこのなかに収まってます｡

Googleから削除したファイルに加えiTunesの音楽データなどを合わせて保管したら､容量は220GB､ファイル数5万強とスゴイ量のバックアップになってしまいました｡とはいえ､これでも月200円程度で済むと思えば安いものです｡

むしろその値段で｢あの[NASA](http://www.publickey1.jp/blog/12/6aws_reinventday1.html)や[NASDAQ](http://aws.amazon.com/jp/solutions/case-studies/nasdaq-omx/)も使うS3に個人データを預けられてる｣という安心感が買えてしまうことに驚きです｡万人向けではありませんが､世の家庭内SEの皆さんにはぜひ試してみていただきたいです｡

## 考察など

先月下旬にデータ整理を思いついてからはや1ヶ月､ようやっとひと通り片付けることができました｡その最中､あちこち寄り道､遠回りしつつ以下の様なことを考えていました｡

1.	手間を掛けない

	｢継続は力なり｣とはよく言ったものですが､バックアップの場合は｢継続できないと命取り｣なわけです｡いざデータを復旧しようとしたら､｢何ヶ月も前にとったバックアップしかないよ．．｣とならないようにしなければなりません｡

	[「3-2-1 ルール」](http://lifehacking.jp/2010/08/3-2-1-backup-rule/)なども参考にしつつ､自分のルールに従って淡々とバックアップしましょう｡バックアップの形態に応じてツールはよりどりみどり､ぜひ自分に合うものを探してみてください｡ちなみにWindowsでのオススメは[CloudBerry Backup for Windows](http://www.cloudberrylab.com/amazon-s3-microsoft-azure-google-storage-online-backup.aspx)です｡

1.	｢何を残さないか｣を決める

	あれもこれもと欲張るにはスペースが足りなさ過ぎますし､作業時間も有限です｡思い切って､バックアップしないデータの仕分けをしてみました｡バックアップを段階分け[^03]し､うまく線引して｢消えても後悔しないデータ｣を切り離すことが大事かと｡

	当面､｢消えてもまた入手できるデータ｣はバックアップせずにまわしてみます｡
	
1.	安心にはお金がかかる

	今回の大掃除では新たにNASを一台購入､またクラウドストレージとしてAmazon S3に利用料金を払い続けることにしました｡初期費用だけで1万数千円かかりましたが､散らばっていたデータを掃除できたさっぱり感､幾重にもデータを保護できている安心感など価値のある出費だったと思ってます｡

	また､消えたら二度と入手できないデータ､特に写真データを､Amazon S3というまさに**｢その道のプロ｣**に預けられたわけですから､毎月数百円の出費は安いものです｡自宅だと停電や天災など以外にも､**｢家族｣**が思わぬ障害要因になったりしますから．．[^05]

------------------

先月の思いつきから始まった｢年末データ大掃除企画｣､番外編や調べたことのメモなどを含めると5つもの記事になりました｡本を自炊したりするともっと色々出てきそうですが､それはまたいずれ｡重い腰を上げたおかげで､データのことを心配せずに済む日々をしばらく送れそうです｡

[01]:http://blog.harupong.com/2012/11/year_end_cleanup_of_my_data/

[02]:http://picasaweb.google.co.jp

[03]:http://d.hatena.ne.jp/dkfj/20121127/1354031314

[^02]:アップロード速度が200~500kbps､全データのアップロードに丸40日もかかると言われるとね．．

[^03]:単一の｢3-2-1ルール｣を全データに適用する必要はないよね､という話です｡データの複製はいっぱい取るけど､手間暇かかるオフサイトバックアップは大事なデータだけ､という発想でやってます｡

[^05]:妻が掃除機の先をグッと押し込んだ先に外付HDDがあったり､息子が放り投げたおもちゃがNASをなぎ倒したり．．(涙)
