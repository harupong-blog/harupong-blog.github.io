---
layout: post
title: 勘違いしないようにしたい、Amazon Glacierのデメリットあれこれ
tags:
- aws
- Dropbox
- fastglacier
- glacier
- life
status: publish
type: post
published: true
meta:
  _edit_last: '2'
---
- [1GBが約1円/月のAmazon Glacierへ簡単にバックアップ＆同期できるフリーソフト「FastGlacier」 - GIGAZINE](http://gigazine.net/news/20121203-fastglacier/)

先日[GIGAZINE][01]でこんな記事が公開されてました｡反応を見ていると｢写真の保管に使えそう｣｢消せないファイル置き場にいいね｣などと皆さんの印象はよさそうですが､実はこれ､｢転んでも泣かない方｣専用です[^01]｡

注意喚起として､転びそうなポイントを挙げておきます｡

## ダウンロードは二重の｢有料制｣

[公式サイト](http://aws.amazon.com/jp/glacier/pricing/)によると､データをダウンロードするにはデータ取り出し料とデータ転送料の2種類の費用を払う必要があります｡

前者は無料範囲を越えると0.01USドル～/GB､後者は～0.12USドル/GBということで､例えば100GBのアップロード済みデータを全てダウンロードすると

一ヶ月かけてゆっくりダウンロードする
:	12.83USドル

回線速度が許す限り急いでダウンロードする
:	 191.58USドル

と､値幅はあれど結構な金額が課金されます｡**｢保管料安くする分､取り出しの特急料金はしっかりいただきます｣**な仕様になってるのでご利用は計画的に｡

## ファイル名などは一切保持されない

GIGAZINEで紹介された[FastGlacierのヘルプページ](http://fastglacier.com/fastglacier-metadata-format.aspx)に以下の記載があります｡

> As you probably know, Amazon Glacier doesn't support file names. Each file (an 'archive') has a unique ID and an optional description (an 'archive description').  
FastGlacier and many other Amazon Glacier tools use archive description to store filenames and other metadata such as last modification time.  
Unfortunately there is no single standard on storing metadata. FastGlacier tries to support most of the common metadata formats.

> (意訳)Amazon Glacierはファイル名を保持しません｡FastGlacierはじめ多くのツールは､Amazon Glacierの備考データにファイル名を記録します｡しかし､残念ながらその記録方法は統一されていません｡

これもサービスを安価にする工夫の一つなのですが､Amazon Glacierではファイル名や更新日などが保持されません｡そういった情報の管理は利用者に委ねる仕様になっています｡

つまり､FastGlacierでアップしたファイルはFastGlacierでダウンロードしないと元のファイル名が得られませんし､そもそも他のツールでAmazon Glacier上のファイルを参照しても以下のような文字の羅列に襲われて悲しい気持ちになってしまう､ということです｡

![glacier filename](http://lh6.googleusercontent.com/-clu_h_eD1Ao/UMWPel9-frI/AAAAAAAAAaA/3w_Pet1jQtA/s640/glacierfilename.jpeg)  
↑FastGlacierでアップしたファイルをCloudberry Explorerで参照した画面｡左側のNAME(ファイル名)列に並ぶ呪文のような文字列が目に痛い．．

## Write Once Read Many､上書きできません

[Amazon Glacier公式FAQ](http://aws.amazon.com/jp/glacier/faqs/#What_is_an_archive)にもあるとおり､アーカイブは変更不可能､上書き保存ができません｡

例えば2GBの動画ファイルを少しだけ編集しても､上書き保存ができないので2GB全部をちまちまとアップロードし直すしかない､ということです｡これはなかなか苦痛[^02]．．

-------------

そもそもビジネス用途なAmazon Web Serviceですが､そのなかでもAmazon Glacierかなり極端なサービスであるだけに､｢100GB保存しても月100円以下！安い！！｣と手を出すとヤケドする可能性大です｡

用法､容量を守って正しく計画的に使うようにしましょう｡そこ､転んでも泣かないように！！

[01]:http://gigazine.net/

[^01]:実際に転びましたがなにか？

[^02]:Dropboxなどのサービスの場合､差分バックアップ等の仕組みで｢変更されたデータ｣のみをアップロードしてくれます｡
