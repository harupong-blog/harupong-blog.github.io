---
layout: post
title: "らじる★らじるの番組情報等メタデータ"
date: 2013-10-24 00:00
comments: true
categories: 
---

[NHKラジオのネット配信「らじる★らじる」を録音するツールを Ruby で作った - Pieces Of Peace](http://blog.harupong.com/2013/10/ripdiru-my-first-ruby-gem/) で利用している情報のメモ。

---------------------------

[らじる★らじるのトップページ][66]へのアクセス時に読み込まれる Javascript が2つ。らじる★らじる固有の処理は後者に含まれている。

1. [http://www3.nhk.or.jp/netradio/files/js/jquery-1.9.1.min.js](http://www3.nhk.or.jp/netradio/files/js/jquery-1.9.1.min.js)
2. [http://www3.nhk.or.jp/netradio/files/js/common.js](http://www3.nhk.or.jp/netradio/files/js/common.js)

common.js の以下の記述

{% codeblock %}
/* config読込 */
config.get(dirplus+"/netradio/app/config_pc.xml");
{% endcodeblock %}

より、[config_pc.xml](http://www3.nhk.or.jp/netradio/app/config_pc.xml) かららじる★らじるの設定情報が読み取れることが分かる。使えそうなのは以下の4つ。

1. 各地域のストリームURL
1. ただいま放送中の番組を取得するAPIのURL
1. 番組表を取得するAPIのURL
1. 番組の詳細情報を取得するAPIのURL

各項目について、2013/10/22時点で分かることをメモ。

## 各地域のストリームURL

第1・第2・FMの3チャンネル分が rtmp と mms のプロトコルで、仙台・東京・名古屋・大阪の4地域分提供されている。ただし、NHK第2のストリームは4地域共通で重複しているので、実際は全部で18ストリームになる。

以下は東京の3チャンネル分6ストリーム。他の地域も `config_pc.xml` に記載されてる。

### rtmpプロトコル
	rtmpe://netradio-r1-flash.nhk.jp/live/NetRadio_R1_flash@63346
	rtmpe://netradio-r2-flash.nhk.jp/live/NetRadio_R2_flash@63342
	rtmpe://netradio-fm-flash.nhk.jp/live/NetRadio_FM_flash@63343

### mmsプロトコル
	http://mfile.akamai.com/129931/live/reflector:46032.asx?bkup=46037&prop=a
	http://mfile.akamai.com/129932/live/reflector:46056.asx?bkup=46060&prop=a
	http://mfile.akamai.com/129933/live/reflector:46051.asx?bkup=46055&prop=a

## ただいま放送中の番組を取得するAPIのURL

`config_pc.xml` に記載されている URL と、 

    http://www2.nhk.or.jp/hensei/api/noa.cgi?c=3&wide=1&mode=jsonp

ネット上でよく言及されてる URL

    http://cgi4.nhk.or.jp/hensei/api/sche-nr.cgi?tz=all&ch=netr1&date=2013-10-24
    http://cgi4.nhk.or.jp/hensei/api/sche-nr.cgi?tz=all&ch=netr2&date=2013-10-24
    http://cgi4.nhk.or.jp/hensei/api/sche-nr.cgi?tz=all&ch=netfm&date=2013-10-24

の2種類がある(以下、前者を公式、後者を非公式と呼ぶ。)。

公式
: 位置情報自動判定。「現在放送中の番組」「一つ前に放送した番組」「次に放送する番組」の3番組情報が、3チャンネル分返ってくる。チャンネルと日付指定不可。

非公式
: チャンネルと日付が指定可。日付指定なしだと当日分になる。指定内容で一日分の番組情報(朝5時からの24時間分)が返ってくる。日付指定は当日以降7日間(計8日分)有効。[^01]

## 動作しなかったAPI

### 番組表を取得するAPIのURL

    http://www2.nhk.or.jp/hensei/api/sche.cgi?c=3&mode=jsonp

### 番組の詳細情報を取得するAPIのURL

    http://www2.nhk.or.jp/hensei/api/sche.cgi?c=3&mb=1&mode=jsonp

## 参考にしたサイト

[radikomemo - foltia - Trac][30]

[30]: http://www.dcc-jpl.com/foltia/wiki/radikomemo
[66]: http://www3.nhk.or.jp/netradio/

[^01]: なお、チャンネルは東京の3チャンネル分だけ指定可能の模様。