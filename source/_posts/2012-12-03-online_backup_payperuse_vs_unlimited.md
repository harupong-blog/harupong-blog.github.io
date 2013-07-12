---
layout: post
title: 年末データ大掃除(番外編) 容量だけじゃない、オンラインバックアップサービスの大事なあれこれ
tags:
- amazon
- crashplan
- glacier
- life
- online backup
- s3
status: publish
type: post
published: true
meta:
  _edit_last: '2'
---
![onlinebackup](http://lh3.googleusercontent.com/-cSgT7dXEtoo/ULxC2Q6NzMI/AAAAAAAAAXY/5d5GPyOWMuw/s400/online-backup.jpeg?gl=JP)  
via [Cloud Backup Reviews](http://www.cloudbackupreviews.com/)

[個人データの年末大掃除！！][01]と題して[Crashplan][02]に個人データ230GB程度をせっせとアップロードしていたのですが､スタート当初は1.5Mbps～2.0Mbps出ていたアップロード速度が､全体の3割程度をアップロードした段階で0.4～0.5Mbpsへと大きく減速｡速度4分の1ということは所要時間4倍になるわけで､**残り時間：39.2日**などという表示を見て激しくやる気減退｡

40日間もパソコンつけっぱなしになんてできるか！！ということで､カッとなって他サービスに浮気してみました｡結果いろいろと気付きがあったのでメモしておきます｡

## オンラインバックアップ､容量無制限タイプ(Crashplan)と従量課金タイプ(Amazon S3/Glacier)の比較あれこれ

以下､容量無制限タイプの[Crashplan][02]と従量課金タイプの[Amazon S3/Glacier][03]を主観的に比較してみました｡素のままだと後者が高級すぎて比較にならないので､[安価にアーカイブする機能][10]を使用して比較してます｡

アップロード/ダウンロード速度優先なら【Amazon S3/Glacier】
:	上述のとおり､回線速度は圧倒的にAmazon S3/Glacierに軍配があがります｡定額容量無制限でやっているCrashplan､他のところで制限をかけないといけないのは致し方なし､といったところでしょうか｡Pogoplug CloudやBackblazeといった類似の容量無制限定額タイプのサービスでも状況は似たり寄ったり､のようです｡

費用優先なら【Crashplan】
:	**｢容量無制限の定額制｣**この言葉だけで無条件にこちらを選択される方も大勢いらっしゃるのではないでしょうか｡定額制の安心感は他に代えがたいものが確かにあります｡一方､対象データが500GB未満の場合はAmazon S3/Glacierの方が安くあがりますのでデータ量と相談ですね｡

とっつきやすさなら【Crashplan】
:	無料の専用ソフトウェアでスマートフォンでの利活用含めすべてが完結するCrashplan､この簡単さは両親にも安心して勧められるほどのお手軽さ｡Amazon S3/Glacierはそもそも企業向けということもあり､ユーザーにはちっともやさしくありません｡

サービスの使い勝手なら【Crashplan】
:	ファイルの更新履歴も個別に取得できる､すぐにファイルをダウンロードできる(iPhoneからも可)など､高機能｡｢容量無制限なDropboxないのかな～｣な欲張りさんはこれで決まりです｡

## 従量課金なオンラインバックアップサービス､Amazon S3/Glacierを試してみた

次にAmazon S3/Glacierの詳細レポート｡予め断っておきますが､｢容量無制限､難しいこと考えなくていいDropboxみたいなの欲しいなぁ｣な方にこれはオススメできません｡転んでも泣かない方向けです｡

### アップロード速度がCrashplanの6倍！早い！！

Amazon S3/Glacierでは一貫して3～4Mbps程度のアップロード速度が出ていました｡回線のアップロード速度が5Mbps程度であったことを考えると､十分合格点なスピードです｡24時間アップロードし続けるとISPの制限にひっかかってしまいそう｡

コンシューマー向け､定額無制限なCrashplanと比較するのは可哀想な感もありますが､それでもやはりこのスピードは素晴らしい｡

### Windows/Mac､どちらも便利なソフトウェアがあった！

[公式サイト][03]を読んだ印象は｢プログラミングができないと使えなさそう．．｣でしたが､サードパーティからいくつか専用ソフトが出ているようです｡今回はWindowsを試してみました｡ざっくり紹介しておきます｡オススメはWindowsの[Cloudberryのソフトウェア][07]｡画面がゴチャッとしてる感はありますが､日本語表示なのでこれ一択！なWindowsユーザーの方もいらっしゃるのでは｡

#### Windows

1.	[CloudBerry][07]

	FTP風にファイルを転送できる[CloudBerry Explorer][13]とスケジュール機能がある[CloudBerry Online Backup][14]など､色々とS3関連ツールを作っている会社｡いずれも29.99ドルの有料版｡初心者にやさしいUI､スケジュール機能､日本語対応｡サイトは英語ですがソフトは日本語対応しています｡

1.	[FastGlacier][06]

	Amazon Glacier専用ソフト｡機能制限Free版と29.99ドルの有料版｡シンプルなUI､軽快な動作が魅力｡英語のみ､スケジュール機能がないので､軽快な動作優先､全て自分でやりたい､トラブったら自分で解決できる人向け｡

#### Mac

1.	[Arq][08]

	試してないのでコメントできないのですが､[評判はよさそう][09]です｡Mac界隈では定番ソフトみたい｡

### 安いがゆえの短所有り｡使い方には注意が必要

[こちらでしつこく考察されているのでぜひ一読をおすすめします][11]が､サービスを安くするために一切を削ぎ落したAmazon Glacierは色々と不便でありました｡例えば最新のファイル一覧を参照するのにも時間がすごくかかるし､ファイル名なども管理の簡易化のため全て削除されてしまいます｡また､WORMなシステムでファイルの上書きができないので､ちょっとでも変更が入るとデータ全てをアップロードする必要がありました｡

そんな欠点をうまいこと補ってくれるのがAmazon S3のアーカイブ機能｡S3にアップロードしたファイルを全てGlacierにアーカイブするように設定すれば､S3の高機能さとGlacierの安価さを組み合わせて利用することが可能です｡実際そのようにして使っていますが､ほぼAmazon Glacierの費用だけで済んでいます｡

---------------------------------------------------------------------------

だらだらとまとまりのない文章を最後まで読んで頂きありがとうございます｡家族や友人にオススメを聞かれれば間違えなく｢Crashplan｣と言いますが､使い勝手が圧倒的にいいので自分はAmazon S3/Glacierを使う､という矛盾した状態が暫く続きそうで文章も着地点が見つかりません．．

こんなのあるよ､ここはどういうこと？などありましたら､ぜひ[Twitter][12]などで教えてください｡

[01]:http://blog.harupong.com/2012/11/year_end_cleanup_of_my_data/
[02]:http://www.crashplan.com
[03]:http://aws.amazon.com/jp/S3/Glacier/
[04]:http://aws.amazon.com/jp/S3/Glacier/faqs/#What_is_an_archive
[05]:https://www.crashplan.com/consumer/store.vtl
[06]:http://fastglacier.com/
[07]:http://www.cloudberrylab.com/
[08]:http://www.haystacksoftware.com/arq/ 
[09]:http://www.marco.org/2012/11/07/arq-adds-S3/Glacier
[10]:http://www.publickey1.jp/blog/12/amazon_s3amazon_glacier.html
[11]:http://d.hatena.ne.jp/dkfj/20121127/1354031314
[12]:http://twitter.com/harupong
[13]:http://www.cloudberrylab.com/free-amazon-s3-explorer-cloudfront-IAM.aspx
[14]:http://www.cloudberrylab.com/amazon-s3-microsoft-azure-google-storage-online-backup.aspx

[^01]:Write Once Read Many｡一度アップロードしたら編集･上書きはできない｡

[^02]:上記のツールでは､更新すべきファイルは旧ファイルの削除の新ファイルの新規アップロード､という2作業をもって更新されるようです｡

[^03]:容量無制限､パソコンは1台のプランで月極払いにした場合の料金｡
