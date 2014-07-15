---
layout: post
title: "Apple のアプリ審査はアクセシビリティをテストすべき"
comments: true
categories: translation
---

以下の文章は、[Marco Arment](https://twitter.com/marcoarment) さんによる [Apple’s App Review Should Test Accessibility – Marco.org](http://www.marco.org/2014/07/10/app-review-should-test-accessibility) の日本語訳です。翻訳をご許可いただきありがとうございました。

The following is the Japanese translation of [Apple’s App Review Should Test Accessibility – Marco.org](http://www.marco.org/2014/07/10/app-review-should-test-accessibility) by [Marco Arment](https://twitter.com/marcoarment).  Thank you Mr. Arment, for [letting me translate](https://twitter.com/marcoarment/status/488871415240605696) his writing.

--------------------------------------------


<!--original
Apple’s App Review Should Test Accessibility
=================================================================
-->
## Apple のアプリ審査はアクセシビリティをテストすべき


<!--original
[Christina Farr’s Reuters article](http://www.reuters.com/article/2014/07/09/us-apple-mobilephone-accessibility-idUSKBN0FE12Q20140709) is pretty bad, as helpfully detailed by [John Gruber](http://daringfireball.net/linked/2014/07/10/reuters-accessibility). Apple’s accessibility support leads the industry by a mile.
-->
[Reuters の記者 Christina Farr が書いた記事](http://www.reuters.com/article/2014/07/09/us-apple-mobilephone-accessibility-idUSKBN0FE12Q20140709) はひどい。どれほどひどいか知りたい人は、[John Gruber](http://daringfireball.net/linked/2014/07/10/reuters-accessibility)のブログ記事を読んでみるといい。Apple が提供しているアクセシビリティ関連の機能は、業界標準のずっと先を行ってる。

<!--original
But the actual issue, buried in the sloppy article, is legitimate:
-->
一方、そのひどい記事からは読み取りにくいけれど、問題が残っていないわけではない。

<!--original
> But when apps don’t work, life can grind to a stop. Jonathan Lyens, a San Francisco city employee, who is legally blind, has a hard time browsing jobs on professional networking site LinkedIn.
>
> “The app is insane. Buttons aren’t labeled. It’s difficult to navigate,” said Lyens. When it comes to social media apps, new problems arise with every release, he said. “I get nervous every time I hit the update button.” …
>
> Now, Apple and Google both have developer guidelines on how to make features accessible, such as labeling buttons that can be read by Apple’s VoiceOver software.
>
> But they don’t require accessibility, in contrast to other strictly enforced rules, such as a ban on apps that present crude or objectionable content. Nor do they offer an accessibility rating system, which some disabled advocates say would be a big help.
-->
> アプリがうまく使えないと、生活がまわらなくなってしまうこともあるです。Jonathan Lyens さん(サンフランシスコ市に雇われていて、重度の視覚障害認定を受けている)は、職業検索サイト LinkedIn での職探しにとても苦労しています。

> 「あのアプリはひどいよ。ボタンがラベル付けされてないから、画面操作がすごく難しい。」Lyensさんによれば、ソーシャルメディアアプリにいたっては新しいバージョンのリリースごとに別の問題が生じるようで、「アップデートボタンを押すとき、いつも不安なきもちになってる」そうです。

> いまでは、Apple や Google が開発者向けガイドラインでアプリのアクセシビリティを高める方法を示しています。ガイドラインには、ボタンにラベル付けをしておけばApple の VoiceOver が読み上げてくれる、などの内容が含まれています。

> けれども、両社ともにアクセシビリティを必須とはしていません。一方、下品なコンテンツを含むアプリを禁止するなどのルールを厳密に課しています。アクセシビリティを評価する仕組みもありません。関係者からは、それがあれば大きな違いになるのに、といった意見も出ています。

<!--original
Apps with standard UIs get most accessibility for free, Apple doesn’t reject apps for inaccessibility, and most customers don’t rely on accessibility tools, so most developers never hear about accessibility problems.
-->
標準的なアプリのUIはそのままでアクセシビリティが最高レベルになる。Apple はアクセシビリティの低さを理由にアプリを拒否したりしないし、お客さんの大半はアクセシビリティの仕組みに依存していないので、ほとんどの開発者にとって、アクセシビリティに関する苦情を耳にする機会は生まれなくなる。

<!--original
In the rush to get apps and updates out the door, it’s easy to forget to test every UI change with VoiceOver. I’ve certainly forgotten many times.
-->
しかも、アプリのリリース、アップデートの提供を急ぐあまり、UI変更の VoiceOver 対応をテストするとき、変更のいくつかを見過ごしてしまうことはよくある。自分だって、何度も見過ごしてしまったことがある。

<!--original
But while accessibility problems only affect a small percentage of an app’s userbase, their impact can be extremely damaging or fatal to those customers’ ability to use the app.
-->
けれど、アクセシビリティの問題がアプリの利用者数のわずかにしか影響しないとしても、実際に影響を受けるお客さんからすれば、「アプリが使えなくなる」という被害は甚大だ。致命的な被害になることだってある。

<!--original
Accessibility failures should be *embarrassments* to all developers because they’re usually *very* easy to fix. For most problems, you just need to add label text to a custom control or image button. Rare “complex” issues are usually less than an hour’s work.
-->
アクセシビリティの欠陥は、開発者にとって**恥じるべきこと**だ。というのも、その欠陥はたいてい**すぐに**直せるものだからだ。問題の大半は、カスタムコントロールやイメージボタンにラベルテキストを付与するだけで解決できる。めったにないような「複雑な」問題でも、1時間もかからず解決できるだろう。

<!--original
I try hard to get accessibility right… when I remember to. My triple-tap home-button shortcut is always mapped to VoiceOver so I can easily test. I include VoiceOver users in betas whenever possible and had an extremely valuable and insightful accessibility review in the WWDC labs this year. But I *still* occasionally ship unlabeled buttons, hidden-view clutter, or inaccessible custom views.
-->
個人的には、アクセシビリティを正しく提供できるよう努力しているつもりだ(忘れることもあるけれど)。自分が使うデバイスの「ホームボタン3度押し」ショートカットは、すぐにテストできるよう、いつも VoiceOver に設定してある。VoiceOver の利用者もベータテストに可能な限り入ってもらうようにもしている。今年の WWDC のラボでは、思慮に富んだとてもありがたいレビューをアクセシビリティ絡みで受けることができた。それでも、ラベルなしのボタン、埋もれてしまったビュー、アクセシビリティ機能から参照不可なカスタムビューを**ときどき**リリースしてしまう。

<!--original
Poor or broken accessibility is exactly the sort of problem that Apple’s App Review team *should* check for: many developers forget to test it, it’s easy for Apple to quickly test when reviewing each app, and it’s easy to fix.
-->
実装が下手、もしくは用をなさないアクセシビリティ機能こそ、Apple のレビューチームが気をつけるべき問題だ。それは、開発者の多くがテストするのを忘れるが、レビュー時に一緒にテストしてしまうのは Apple にとっては容易だし、不備の修正も簡単だ。

<!--original
It’s even more clear when considering the customers’ point of view. App Review assures customers of minimum quality and security standards so they feel comfortable buying apps, and [we all benefit](http://www.marco.org/2011/02/04/ode-to-the-app-review-team) from it. The [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) are quite clear on the basics:
-->
お客さんの目線に立ってみれば、それはより明確になる。Apple がアプリをレビューするから、最低限の品質とセキュリティが確保されていてお客さんは安心してアプリを買えるし、[開発者にとっても得るものは多い](http://www.marco.org/2011/02/04/ode-to-the-app-review-team) 。
 [App Store のアプリケーション審査ガイドライン](https://developer.apple.com/appstore/resources/approval/guidelines.html) によると、審査の基本はとてもわかりやすい。

<!--original
> **2.2** Apps that exhibit bugs will be rejected
>
> **2.3** Apps that do not perform as advertised by the developer will be rejected
-->
> **2.2** バグがあるアプリはリジェクトします
>
> **2.3** 開発者の申請どおりに動作しないアプリはリジェクトします

<!--original
Of course. Customers should know that they’re getting what’s promised.
-->
そりゃそうだ。お客さんは、「謳い文句どおりのアプリが手に入るんだ」という認識を持っていいはずなんだ。

<!--original
But those rules aren’t applied to accessibility. For a customer who uses VoiceOver, rows of unlabeled buttons and inescapable screens are “bugs”, and an app with inaccessible features certainly does not “perform as advertised”.
-->
上述のルールはアクセシビリティには当てはまらないが、かといって VoiceOver を使ってるお客さんにとっては、ラベルなしのボタンや回避不可能な画面は「バグ」に他ならないし、アクセス出来ない機能があるアプリは「謳い文句どおりのアプリ」とは言いがたい。

<!--original
This sucks for everyone: Apple, developers, and most of all, customers relying on accessibility aides who unknowingly pick a bad app, can’t do what they need, and now can’t trust the App Store with future purchases.
-->
こうなってしまうと、誰(Appleや開発者やお客さんたち)のためにもならない。特にアクセシビリティ機能に依存しているお客さんたちの場合がひどくて、そうと知らずにひどいアプリを選んでしまった場合、やりたいこともできなくなって、今後アプリを買うときには App Store を信用できなくなってしまうだろう。

<!--original
Requiring all apps and games to be completely accessible is probably infeasible. But that’s not the only option. My proposed fix:
-->
ただ、ゲームを含むすべてのアプリに完璧なアクセシビリティを要求するのはおそらく無理難題だろう。とはいえ、他にもやり方はある。以下のようにすればいい。

<!--original
1.  Allow developers to *opt into* accessibility testing for each submission in iTunes Connect. Put it on the screen that asks about cryptography so all developers must answer it and are made aware of it.
2.  Show a small badge on each app’s page in the Store that passes accessibility testing. This helps customers make buying decisions for their needs.
-->
<!--original
    Passing requires all advertised functionality to be accessible, all accessible controls to have accurate labels, and no navigational traps such as inescapable screens or stuck states.
-->
<!--original
3.  If a user has VoiceOver enabled while downloading an app that has *not* been tested for accessibility, or while updating a previously tested app to to an untested version, show a warning dialog and ask them to confirm whether they still want to proceed. This helps them *and* gives developers a good reason to opt into accessibility testing.
-->
1. 開発者がアクセシビリティのテストを受けたい場合、アプリを申請する度に iTunes Connect でテストに申し込めるようにする。暗号化関連の画面で申し込めるようにすれば、申し込みの要否を必ず入力することになって、開発者が自覚できる。
2. App Store のアプリのページにバッジを出す。それはアクセシビリティのテストを通過したことを意味し、お客さんがアプリ購入を検討するときの判断材料になる。  
テストを通過するには、以下が順守されなければならない。謳われている内容がアクセシビリティを利用してすべてアクセス可能であること。利用可能なコントロールすべてに正確なラベルが付与されていること。回避不可能な画面や画面遷移中の行き止まりといった「UIナビゲーション上の罠」がないこと。
3. 利用者が VoiceOver を有効にしている場合、アクセシビリティのテストを受けていないアプリのダウンロードや、テスト済みバージョンから未テストバージョンへのアップデート時に警告を出す。ユーザーは警告を受けて、そのまま続けるか中止するかの意思表示をする。該当者にとっても有意義だし、開発者にとってもアクセシビリティのテストに申し込むいいきっかけになるだろう。

<!--original
There’s definitely more Apple can do to address this very real problem, and a system like this would make a huge difference.
-->
現在進行形のこの問題に対して Apple ができることはたくさんあるし、ここに書いたような仕組みがあれば状況は劇的に改善されるだろう。

-------------
初出公開：　2014年7月15日、　最終更新日： 2014年7月15日
