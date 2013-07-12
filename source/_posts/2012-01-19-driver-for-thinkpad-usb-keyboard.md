---
layout: post
title: ThinkPad USBトラベルキーボードを会社で使おうとしたらドライバーがなくてハマったので対策まとめ
tags:
- life
- PC
- ThinkPad
status: publish
type: post
published: true
meta:
  _edit_last: '2'
---
<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:51CF81A4-8F44-4a2c-8837-198C090B9994:e7567e61-ac40-4cd4-9cbe-660a0d082445" class="wlWriterEditableSmartContent"><p><a href="http://nightmare.no-blog.jp/nightmare/2009/09/thinkpad_usb_78.html" atomicselection="true"><img style="border-right: 2px; border-top: 2px; border-left: 2px; border-bottom: 2px" height="450" src="http://lh3.ggpht.com/-wH29HMxHihw/T9XPAPyRD9I/AAAAAAAAAJg/ii8ZLI0yZqk/s600/photo12.jpg" width="600"></a></p></div> <p>新卒で入った会社で最初に支給されたPCがThinkPad､以降トラックポイント(キーボード中央の赤いポッチ､これでマウス操作ができる)から離れられないThinkPad Loverです､こんにちは｡</p> <p>最近支給された会社用のPCがWindows7なDELLのデスクトップ､困ったときの外付けキーボード <a href="http://www.amazon.co.jp/Lenovo-ThinkPlus-USB%E3%83%88%E3%83%A9%E3%83%99%E3%83%AB%E3%82%AD%E3%83%BC%E3%83%9C%E3%83%BC%E3%83%89-%E3%82%A6%E3%83%AB%E3%83%88%E3%83%A9%E3%83%8A%E3%83%93%E4%BB%98-31P9514/dp/B0002DOSQW" target="_blank">Thinkpad USBトラベルキーボード ウルトラナビ付[31P9514]</a> で問題なし！と意気揚々接続するもトラックポイント認識せず．．．｢ドライバーが悪いんだろうなぁ｣とグーグル先生が教えてくれた情報はことごとく賞味期限切れ．．．</p> <p>ということで､2012年1月時点で有効な方法をメモ｡Windows 7 32-bitと64-bit両方OK｡他モデルのThinkPad USBキーボードも対応してるようです｡</p> <h2>1. 下記URLから、ドライバーのインストーラーをダウンロード</h2> <p>v2kyb03us17.exeとv5kyb04us17.exeの2ファイル｡<br><a href="http://support.lenovo.com/en_US/downloads/detail.page?DocID=DS004171">http://support.lenovo.com/en_US/downloads/detail.page?DocID=DS004171</a></p> <h2>2. インストール作業は2段階</h2> <p>1.でダウンロードした2ファイルをそれぞれ実行すると必要なファイルがコピーされます｡コピーされたファイルのうち､以下のディレクトリにあるsetup.exeを実行することで、ドライバーがインストールされ､UltraNavメニューがタスクバーに表示されるようになります。</p> <p>1) C:SWTOOLSDRIVERSUnavkeyboardv2kyb03us17<br>2) C:SWTOOLSDRIVERSUnavkeyboardv5kyb04us17</p> <p><a href="http://www.amazon.co.jp/review/R1P7RM1XR39Y3Q/ref=cm_cr_dp_perm?ie=UTF8&amp;ASIN=B0002DOSQW&amp;nodeID=2127209051&amp;tag=&amp;linkCode=" target="_blank">Amazonのレビュー</a>に｢公式ドライバーをインストールしても動かない！｣というのがありましたが､おそらくsetup.exeの方を実行してないんじゃないかなぁ｡</p>
