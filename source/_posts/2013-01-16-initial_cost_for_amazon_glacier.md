---
layout: post
title: クラウドのうえのオフラインストレージ、Amazon Glacierを使うための初期費用は【1万ファイルにつき0.5ドル】くらい
tags:
- aws
- glacier
- life
- s3
status: publish
type: post
published: true
meta:
  _edit_last: '2'
---
タイトルが結論で他に語ることはありませんが､もしお時間あれば以下の駄文もお付き合いください｡

先月1ヶ月かけて個人データをAmazonのストレージサービスである[S3][01]と[Glacier][02]に引っ越しました｡同月の請求書が届いたので､実際に掛かった初期費用を見ておきます｡

![glacierInitialCost.jpeg](http://lh3.ggpht.com/-kMII4KOcZAs/UPY5KpxmL5I/AAAAAAAAAew/j_-q40NnQUA/s512/glacierInitialCost.jpeg)

使用量に対する課金は月極従量課金ということで初期費用から省きます｡今回は[利便性を考え][03]､約49,500ファイル､213GBのデータを[S3][01]にアップロードしたあと[Glacier][02]に移行しましたので､

S3へのアップロード費用[^01]
: 54,509リクエストで0.55ドル

Glacierへの移行費用[^02]
: 51,129リクエストで2.56ドル

の合計**3.11ドル**かかりました｡1万ファイル0.6ドルくらいでしょうか｡[S3][01]を省けば[料金表](http://aws.amazon.com/jp/s3/pricing/)どおりの1万ファイル0.5ドルくらいです｡当たり前ですね｡

とはいえ､今後の利用を検討している方は､1GB/0.01ドル/月の月極課金に加え､1万ファイルにつき0.5ドルの初期費用も頭の片隅に置いておくといいでしょう｡海外では数百万のファイルをアップしえらい額の請求が！！という話もありました[^03]｡

## これからはオフラインストレージという言葉が流行るかもしれない

先日､アマゾンデータサービスジャパン株式会社主催の [AWSストレージソリューションセミナー](http://kokucheese.com/event/index/65943/)というのに参加してきました｡お題は

1. Amazon S3, Amazon Glacier(グレイシャー)のご紹介
1. AWS Storage Gateway (ストレージ　ゲートウェイ)のご紹介

の2点だったのですが､前者のスピーカー北迫さんのスライドにしきりと｢オフラインストレージ｣という言葉が出てきたのが印象的でした｡これまでオフラインストレージといえばテープや光ディスクなどのことを指してきたように思いますが､Amazonはその分野もクラウドに持って行っちゃおうとしてるのね､と｡

社内LAN､自社データセンター､といった設備･資産の抱え方を真剣に見なおしてもいい時期に来てそうです｡AWS包囲網によって｢クラウドには◯◯がないから．．．｣という言い訳がひとつずつ潰されている印象｡不可逆と思われるこの流れ､まさに｢インターネットが負ける方に賭けるな｣ですね｡

### 併せて読みたい

引越しのゴタゴタが気になるという方､これらも併せてどうぞ｡

- [勘違いしないようにしたい､Amazon Glacierのデメリットあれこれ | Pieces of Peace](http://blog.harupong.com/2012/12/be_careful_with_amazon_glacier/)
- [ややこしすぎるAmazon Glacierのデータダウンロード料金まとめ | Pieces of Peace](http://blog.harupong.com/2012/12/too_complicated_glacier_pricing_model/)
- [年末データ大掃除(2) ～Amazon S3/Glacierに全てを漏れなく安価に保管できてすっきりした編～ | Pieces of Peace][03]

[01]:http://aws.amazon.com/jp/s3/
[02]:http://aws.amazon.com/jp/glacier/
[03]:http://blog.harupong.com/2012/12/year_end_cleanup_of_my_data_part2/

[^01]:$0.01 per 1,000 PUT, COPY, POST, or LIST requests の部分

[^02]:$0.050 per 1,000 Glacier Requests (US) の部分

[^03]:[Cost of Transitioning S3 Objects to Glacier - Alestic.com](http://alestic.com/2012/12/s3-glacier-costs)
