---
layout: post
title: 「パスワードは全て異なるものにするしかない」でも不安になる現状
tags:
- Internet
- life
- password
- security
- service
status: publish
type: post
published: true
meta:
  _edit_last: '2'
  _sd_is_markdown: '1'
  _wp_old_slug: every-password-made-unique-still-worries-me
---
<p>副題「一元管理は、適切に扱っても｢諸刃の剣｣になりうる」</p>

<h2>前提：共通パスワードはやはり危険｡これはもう異論の余地なし</h2>

<p><a href="http://www.akiyan.com/blog/archives/2012/05/password-unique.html">この記事</a>にある<a href="https://twitter.com/akiyan">@akiyanさん</a>の例え</p>

<blockquote>
  <p>これを現実世界の鍵に例えると、「家の鍵と、貸しロッカーの鍵が同じ」みたいなものです。こう思うとヤバいでしょ？家の鍵穴は秘密でも、貸しロッカーには管理者が別にいて、信用できるかどうかは謎です。</p>
</blockquote>

<p>が秀逸で､鍵が同じということは､</p>

<ul>
<li>貸しロッカーの管理者は他人の家の鍵をいつでも開けられる</li>
<li>自分がいくら家の鍵を厳重に管理しても､貸しロッカーの管理者が鍵を失くしたらどうしようもない</li>
</ul>

<p>というリスクに自らを晒してることに他なりません｡メールやSNS､オンラインバンキングなど､実生活に多大な影響を与えるネット上のツールやサービスを使うための鍵､パスワードに同じ物を使いまわすということは､上述のリスクに自らをネット上でも晒してることになるわけです｡これはまず危険すぎます｡</p>

<a href="http://www.akiyan.com/blog/archives/2012/05/password-unique.html">上に引用した記事</a>を読んで改めてこの事に気付き､同記事で紹介されているような方法にパスワード管理を一元化[^01]｡ここ数ヶ月は<strong>多少の不便さ･手間と引換に大きな安心を得て</strong>各種Webサービスを使ってきました｡

[^01]:KeePassでランダムなパスワードを生成･管理､Dropboxに保存したKeePassデータファイルで複数デバイス間連携

<h2>一元管理≒SPOF(単一障害点)ということの危うさ</h2>

<p>今月に入り一部で大騒ぎになったこの話題→<a href="http://fladdict.net/blog/2012/08/icloud-hack.html">iCloudハック事件の手口がガード不能すぎてヤバイ | fladdict</a> ｡被害者自身による事の顛末記録(英語)は<a href="http://www.wired.com/gadgetlab/2012/08/apple-amazon-mat-honan-hacking/">こちら</a>｡</p>

<p>被害者が有名なネットメディアの記者であったため関係者の動きも早く､結果的には失われたデータ等被害の大部分は復旧することができたようです｡<a href="http://www.wired.com/gadgetlab/2012/08/mat-honan-data-recovery/all/">復旧の様子を被害者自らが綴っている</a>のですが特に印象的だったのが以下の一節｡</p>

<blockquote>
  <p>My Dropbox password was itself a 1password-generated litany of nonsense. Without access to Dropbox, I couldn’t get my keychain. Without my keychain, I couldn’t get into Dropbox.</p>
  
  <p>(訳)僕のDropboxのパスワードも､パスワード管理ツールが生成した無意味な文字の羅列だったんだよね｡Dropboxなしではパスワード管理ツールのデータにもアクセスできないし､パスワード管理ツールのデータなしではDropboxにもアクセスできない｡</p>
</blockquote>

<h3>自分も同じ状況！！！</h3>

<p>ということで対策ですが、</p>

<ol>
<li>パスワード管理ツール周りのパスワードだけは暗記する</li>
<li>必須のパスワードのみ印刷して保管</li>
</ol>

<p>の2つくらいしか思いつきません。当面は1.でしのごうと思っていますが、それもいつまでもつのやら。利便性がもたらした新たなイタチごっこ、今後頭の片隅に置いとく必要がありそうです。</p>
