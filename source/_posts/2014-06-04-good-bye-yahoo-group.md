---
layout: post
title: "Yahoo グループの過去ログを MHonArc でアーカイブ化した "
comments: true
---

Yahoo グループがサービス終了するのは半年近く前からアナウンスされていたので、

[「Yahoo!グループ」サービス終了、システム老朽化で継続困難 -INTERNET Watch][71]

10数年使っていた同窓会的なメーリングリストを [Google グループ][85]へ移行した。

Yahoo グループの Web で過去メールが閲覧できたので、溜まりに溜まった10数年分の過去メールについては同じようなアーカイブを[MHonArc][98]を使って作っておいた。

[MHonArc][98] は Perl 製のメールデータをHTMLアーカイブ化するツールで、Perl が動けば Windows でも使えるみたい。今回は Ubuntu で `apt` を使ってインストールした。ひじょーにサクッと変換できるので、やまほどの EML ファイルを手に途方に暮れてる方は試してみると良いかも[^01]。


[71]: http://internet.watch.impress.co.jp/docs/news/20131218_628285.html
[85]: https://groups.google.com/forum/#!overview
[98]: http://www.mhonarc.org/

[^01]: 変換用のテンプレートファイルは「MHonArc rcfile」などでググれば色々出てくると思う。
