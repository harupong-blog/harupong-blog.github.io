---
layout: post
title: 【翻訳】Twitterクライアントの利用者トークン数制限はどのように適用される？
tags:
- blog
- Internet
- twitter
status: publish
type: post
published: true
meta:
  _edit_last: '2'
  _sd_is_markdown: '1'
---
<h2>非公式Twitterクライアント界隈がプチ炎上中</h2>

<p>8月16日に<a href="https://dev.twitter.com/blog/changes-coming-to-twitter-api">Twitter APIのルール変更が発表</a>されて以来､非公式Twitterクライアントに今後課される制限について､開発者の方を中心に喧々諤々のやりとりが行われます｡発表された情報が断片的であるため､様々な憶測･誤解･誤報が流れているようです｡</p>

有名サードパーティ製TwitterクライアントTweetbotの<a href="http://tapbots.com/blog/">開発者ブログ</a>に､｢誤解を解こう｣としてルール適用の具体例が記載されていました[^1]｡開発者のみならず､利用者も知っておくべき内容が含まれていると思うので､以下に翻訳を掲載します｡

[^1]:自社製Mac版クライアントのパブリックベータ版におけるトークン数制限を巡ってTwitter社とやりとりしたうえで得た情報とのことです｡

あなたがiPhone/Androidで愛用しているSOICHA/ついっぷる/twiccaなどにも関係がある話です[^2]｡一読することを強くオススメします｡

[^2]:スマフォアプリに限らず､WindowsやMacで使うようなクライアントアプリも全て対象です｡

<h2>【翻訳】Twitterクライアントの利用者トークン数制限はどのように適用される？</h2>

<blockquote>
  <ol>
  <li>2012年8月16日をもって､Twitterのサードパーティ製クライアントには利用者トークン数上限が課されます｡その上限は10万トークンとし､もし利用者トークンが既に10万を超えている場合は､同日時点のトークン数の2倍を上限とします｡</li>
  <li>これらの上限はサードパーティ製クライアントにのみ課されます｡クライアントアプリケーション以外に上限は課されません｡</li>
  <li>これらの上限は｢アプリケーション毎｣に課されるため､1開発者が複数のアプリを開発している場合､アプリ毎に上限が課されます｡</li>
  <li>同じアプリ､同じアカウントで複数の端末からログインした場合､利用したアプリが有するトークン数の一つを消費することになります｡</li>
  <li>同じアプリから複数のアカウントでログインした場合､利用したアプリが有するトークン数の複数を消費することになります｡</li>
  <li>Twitter Web版の 設定 -> アプリ連携 から｢許可を取り消す｣をクリックすると､消費したトークンを返却することができます｡</li>
  <li>現時点において､トークンに有効期限はありません｡一度アプリ連携を許可すると､｢許可を取り消す｣を実行しない限りそのトークンは使用中と認識されます｡</li>
  </ol>
</blockquote>

<p>(翻訳終わり｡<a href="http://tapbots.com/blog/news/where-did-the-tweetbot-for-mac-alpha-go">原文</a>)</p>

<h2>ユーザーが意識すべきことは？</h2>

トークン[^3]は

[^3]:使用権､とでも訳せばよいのかな？

<ul>
<li>有効期限がない</li>
<li>連携許可を取り消すと返却できる</li>
</ul>

<p>ので､非公式Twitterクライアントのユーザーとしては､</p>


- アプリ開発者のために､使わなくなったアプリのトークンは上記6.の手順で返却すること[^4]
- 利用中アプリの連携許可を間違って取り消さないこと[^5]

[^4]:そのアプリで利用可能なトークンが増える->新規ユーザーが増やせる､という流れが出来ます｡

[^5]:トークン数が上限に達してしまい再度連携させられない恐れがある

<p>を心がければよさそうです｡iPhoneアプリ探しが好きで過去にあれこれTwitterアプリを試したことがある方､そもそもTwitter Web版の設定画面なんて一度も開いたことがないという方､今すぐ<a href="https://twitter.com/settings/applications">アプリ連携の画面</a>にアクセスして設定を見直しましょう｡</p>
