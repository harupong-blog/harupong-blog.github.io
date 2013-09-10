---
layout: post
title: "ブクログの bookmarklet だと Kindle 本が本棚に追加できないので直してみた"
date: 2013/09/10 00:00
comments: true
categories: 
---

このブログの[読んだ本][8]シリーズは、[ブクログ](http://booklog.jp/)に登録してあるデータを引っ張ってきている。きっかけは@[kentaro さん][48]のブログの[読んだ本をブクログでふりかえる][91]シリーズ[^01]。

ブクログは登録データの編集がやりやすくて気に入ってる[^02]のだけれど、唯一残念なのがタイトルにも挙げた「Kindle 本が bookmarklet で登録できない」というところ。[ブクログ Kindle ストア対応が2012年10月][30]で以後ずっとできてないっぽいし、[Chrome 拡張][67]もあるしでこのままだと対応の望み薄か！？

ということで、重い腰をあげて javascript を眺めてみることに。なお当方、これが初の javascript いじり。

以下、調査経過を含め結構な長文なので、修正した bookmarklet が欲しい方は以下をどうぞ。

<a href="javascript:var d=document, w=window, e=w.getSelection, k=d.getSelection, x=d.selection, s=(e?e():(k)?k():(x?x.createRange().text:0)), f='http://booklog.jp/blet', l=d.location, g=d.getElementById('ASIN')||d.getElementsByName('ASIN.0')[0], a=(g?g.value:''), e=encodeURIComponent, p='?v=2&u='+e(l.href)+'&s='+e(s)+'&a='+e(a), u=f+p, a=function(){w.open(u,'_blank')};if(/Firefox/.test(navigator.userAgent))setTimeout(a,0); else a(); void(0);">ブクログ bookmarklet Kindle 本対応版</a>

<!--more-->

## 直してみた(1) 状況証拠集め
### bookmarklet 実行時挙動の違い

[Amazon.co.jp： 剣客商売 (新潮文庫―剣客商売): 池波 正太郎: 本][5]と[Amazon.co.jp： 剣客商売一　剣客商売: 1 eBook: 池波 正太郎: Kindleストア][45]で bookmarklet を実行し、挙動を調べたところ、URLクエリパラメータ[^03]の部分に違いが一つ見つかった。

クエリパラメータは全部で4つあるのだけど、`a=` で定義されているパラメータが Kindle 本の場合だけ存在しない。a で始まってるし、このパラメータ、おそらく ASIN だろうなぁ、と。で、Amazon での検索に不可欠な ASIN が URLクエリパラメータ として渡されてないのでは！

詳細は以下の通り。

#### うまくいくケース(紙の本の場合)

1. [Amazon.co.jp： 剣客商売 (新潮文庫―剣客商売): 池波 正太郎: 本][5]を表示した状態で bookmarklet を実行
2. 処理用の新しいタブが開く
3. 2.のタブが、1.の本が選択された状態のブクログ登録画面へと遷移
4. 本を登録する

なお、手順2. で開くタブの URL は以下の通り。

{% codeblock %}
http://booklog.jp/blet?v=2&u=http%3A%2F%2Fwww.amazon.co.jp%2F%25E5%2589%25A3%25E5%25AE%25A2%25E5%2595%2586%25E5%25A3%25B2-%25E6%2596%25B0%25E6%25BD%25AE%25E6%2596%2587%25E5%25BA%25AB%25E2%2580%2595%25E5%2589%25A3%25E5%25AE%25A2%25E5%2595%2586%25E5%25A3%25B2-%25E6%25B1%25A0%25E6%25B3%25A2-%25E6%25AD%25A3%25E5%25A4%25AA%25E9%2583%258E%2Fdp%2F4101157316%2Fref%3Dtmm_bunko_meta_binding_title_0%3Fie%3DUTF8%26qid%3D1378786293%26sr%3D1-2&s=&a=4101157316
{% endcodeblock %}

#### うまくいかないケース(Kindle 本の場合)

1. [Amazon.co.jp： 剣客商売一　剣客商売: 1 eBook: 池波 正太郎: Kindleストア][45]を表示した状態で bookmarklet を実行
2. 処理用の新しいタブが開く
3. 画面が遷移せず、ブランクなタブが開いたままになる

なお、手順2. で開くタブの URL は以下の通り。

{% codeblock %}
http://booklog.jp/blet?v=2&u=http%3A%2F%2Fwww.amazon.co.jp%2F%25E5%2589%25A3%25E5%25AE%25A2%25E5%2595%2586%25E5%25A3%25B2%25E4%25B8%2580-%25E5%2589%25A3%25E5%25AE%25A2%25E5%2595%2586%25E5%25A3%25B2-1-ebook%2Fdp%2FB0096PE57E%2Fref%3Dsr_1_2%3Fs%3Dbooks%26ie%3DUTF8%26qid%3D1378786293%26sr%3D1-2%26keywords%3D%25E6%25B1%25A0%25E6%25B3%25A2%25E6%25AD%25A3%25E5%25A4%25AA%25E9%2583%258E&s=&a=
{% endcodeblock %}

### ブクログ提供の bookmarklet

2013/09/10 時点で[公式に提供されている bookmarklet ][35]は以下の通り。

{% codeblock %}
javascript:var d=document,w=window,e=w.getSelection,k=d.getSelection,x=d.selection,s=(e?e():(k)?k():(x?x.createRange().text:0)),f='http://booklog.jp/blet',l=d.location,g=d.getElementById('ASIN'),a=(g?g.value:''),e=encodeURIComponent,p='?v=2&u='+e(l.href)+'&s='+e(s)+'&a='+e(a),u=f+p,a=function(){w.open(u,'_blank')};if(/Firefox/.test(navigator.userAgent))setTimeout(a,0); else a(); void(0);
{% endcodeblock %}

冒頭の `var` で始まる変数宣言部分がセミコロンのところまで延々と続き、そのあとに `if - else` な一文がきて、最後はおまじない的な `void(0)` でおしまい、と。javascript はセミコロンで文と文を区切るそうなので、以後は改行を入れつつ眺めていく。 

## 直してみた(2) if - else 部分を眺める

{% codeblock %}
if(/Firefox/.test(navigator.userAgent))setTimeout(a,0);
else a();
{% endcodeblock %}

先に2行目を見てみると、

**1行目の条件に当てはまらない場合は関数 `a` を実行する**

ということらしい。で、1行目の条件って何かというと、どうも Firefox のみが対象みたい。[setTimeout() ][28]というメソッドを使って、**Firefox のときだけ、0ミリセカンドの遅延を発生させたあとに関数 `a` を実行する**ってことになってる。

なんでこんなことしてるんだろ...不具合対策？

ともかく、if - else 部分で関数 `a` を実行したいってのは分かった。で、肝心の関数 `a` ってなんなのよ！というのはこの次で。

## 直してみた(3) 関数 `a` の正体とは？変数宣言部分を眺める

javascript の変数宣言はカンマで区切れば一括してできるそうなので、カンマごとに改行してみる。

{% codeblock %}
var d=document,
w=window,
e=w.getSelection,
k=d.getSelection,
x=d.selection,

s=(e?e():(k)?k():(x?x.createRange().text:0)),
f='http://booklog.jp/blet',
l=d.location,
g=d.getElementById('ASIN'),
a=(g?g.value:''),

e=encodeURIComponent,
p='?v=2&u='+e(l.href)+'&s='+e(s)+'&a='+e(a),
u=f+p,
a=function(){w.open(u,'_blank')};
{% endcodeblock %}

**関数 `a` キターーー！！ASIN キターーー！！**

ということで、ググっと近づいてきた。

### 関数 `a` の正体

上記14行目が関数 `a` の正体。`function()` で記述が始まる関数を匿名関数といい、このケースでは `{` 以降に続くその中身は `open` メソッド。新しいタブで `u` を開いてね、という意味みたい。

んで、その `u` だけど、13行目にある `f+p`、つまり `http://booklog.jp/blet?v=2&u='+e(l.href)+'&s='+e(s)+'&a='+e(a)` のこと。URLクエリパラメータきましたよっ！！Kindle 本の場合だけ存在しなかった `a=` の部分を見てみると、`e(a)` となってる。

- `e` は `encodeURIComponent` の省略(11行目)
- `a` は 12行目時点では `(g?g.value:'')` (10行目で定義されてる)

もう、`g` って何よ！正解は9行目の `d.getElementById('ASIN')`。文章内から指定した ID の要素を取得するメソッドで、このケースでは1行目の `d`、開いているページのソースから ASIN という ID がつけられている要素をとってくることになる。

で、とってきた要素使って10行目で条件分岐させてるのだけど、ここでよくある if 文じゃなくて `?:` を使ってる。これは、`?` の左側が `true` なら `:` の左側を、`false` なら `:` の右側を、という書き方。それを踏まえてさきほどの `e(a)` を読み直すと、

- ASIN という ID の要素があればその値を `encodeURIComponent` でエンコードした
- そんな要素がない場合は `''` を `encodeURIComponent` でエンコードした

が値が返ってくることになる。Kindle 本の場合URLクエリパラメータが `a=` だけで返ってきていたのは、10行目の条件分岐処理の結果のようだ。

### 消えたASIN を探せ！

[紙の本のページ][5]の HTML ソースで ID が ASIN の要素を探すと

{% codeblock %}
<input type="hidden" id="ASIN" name="ASIN" value="4101157316" />
{% endcodeblock %}

というのが見つかる。おお、ASIN という ID で取得した要素の値、value、がURLクエリパラメータのそれと一致しとる！！

で、次に[Kindle 本のページ][45]の HTML ソースを見てみる。思った通り、ASIN という ID が付与された要素が存在しない。代わりに使えそうなものはないのかっ！！ソースを行ったり来たりしてると、

{% codeblock %}
<input type="hidden" name="ASIN.0" value="B0096PE57E" />
{% endcodeblock %}

というのが見つかった。ASIN.0 って何ですか... ID はないけれど NAME があるので、`getElementsByName` というメソッドが使えそう。これは、指定した NAME と合致する要素をリスト形式で返すメソッドらしい。NAME を指定する記述のあと、`[0]` といった具合でリスト内の位置を指定するとその位置の要素だけ返ってくるようなので、今回のケースでは `d.getElementsByName('ASIN.0')[0]` とすればよさそうだ。

## 直してみた(4) ASIN の値を取得、変数に代入する方法を見直す

変数 g に紙の本の ASINコードを含む要素が入ってるのだから...

1. 変数 gk を作る
2. `d.getElementsByName('ASIN.0')[0]` で取得した要素を代入する
3. `?:` の条件分岐を、g の値か gk の値か、という分岐に変える

という修正方法がパッと浮かんだのだけど、変数増やすのイマイチだなぁと一思案。[Pocket の bookmarklet](http://getpocket.com/add) にある `||` で論理和な以下の記述を見つけた。

{% codeblock %}
var o=t.getElementsByTagName('head')[0]||t.documentElement;
{% endcodeblock %}

この例だと、 `t.getElementsByTagName('head')[0]` が `true` ならそれを、`false` なら `t.documentElement` を変数 o に代入する、という書き方で、これを修正案として採用。ブクログ bookmarklet の ASIN 要素取得部分

{% codeblock %}
g=d.getElementById('ASIN'),
{% endcodeblock %}

を、

{% codeblock %}
g=d.getElementById('ASIN')||d.getElementsByName('ASIN.0')[0],
{% endcodeblock %}

に直してみた ↓ 。

<a href="javascript:var d=document, w=window, e=w.getSelection, k=d.getSelection, x=d.selection, s=(e?e():(k)?k():(x?x.createRange().text:0)), f='http://booklog.jp/blet', l=d.location, g=d.getElementById('ASIN')||d.getElementsByName('ASIN.0')[0], a=(g?g.value:''), e=encodeURIComponent, p='?v=2&u='+e(l.href)+'&s='+e(s)+'&a='+e(a), u=f+p, a=function(){w.open(u,'_blank')};if(/Firefox/.test(navigator.userAgent))setTimeout(a,0); else a(); void(0);">ブクログ bookmarklet Kindle 本対応版</a>

## 直してみた(5) おまじない `void(0)` 部分 

ここまでで当初の目的は達成してるのだけど、せっかくなので最後の一文、

{% codeblock %}
void(0);
{% endcodeblock %}

も見ておく。[The void operator in JavaScript][36] によると、

> bookmarklet を実行して `undefined` 以外が返された場合、開いているページがその返された内容で上書きされる。`void(0)` と書くことで、返る値が `undefined` になり、開いているページに変更が生じなくなる。

とのこと。`void(0)` なしでも Chrome では影響なかったけど、Firefox だと画面が数字の羅列に変わってしまった。その対策ってことなんでしょう。

-------------------

調べてゴリゴリと直すだけでも大変だけど、経過や背景を文章にまとめるのはもっと大変だ...とりあえず直せてよかった。思わぬ形だったけど、少しは javascript を理解できて満足しとります。

[5]: http://www.amazon.co.jp/%E5%89%A3%E5%AE%A2%E5%95%86%E5%A3%B2-%E6%96%B0%E6%BD%AE%E6%96%87%E5%BA%AB%E2%80%95%E5%89%A3%E5%AE%A2%E5%95%86%E5%A3%B2-%E6%B1%A0%E6%B3%A2-%E6%AD%A3%E5%A4%AA%E9%83%8E/dp/4101157316/ref=tmm_bunko_meta_binding_title_0?ie=UTF8&qid=1378786293&sr=1-2
[8]: http://blog.harupong.com/2013/09/books-i-read-on-august-2013/
[28]: http://www.w3schools.com/jsref/met_win_settimeout.asp
[30]: http://info.booklog.jp/?eid=535
[35]: http://booklog.jp/bookmarklet
[36]: http://www.2ality.com/2011/05/void-operator.html
[45]: http://www.amazon.co.jp/%E5%89%A3%E5%AE%A2%E5%95%86%E5%A3%B2%E4%B8%80-%E5%89%A3%E5%AE%A2%E5%95%86%E5%A3%B2-1-ebook/dp/B0096PE57E/ref=tmm_kin_title_0?ie=UTF8&qid=1378786293&sr=1-2
[48]: https://twitter.com/kentaro
[67]: https://chrome.google.com/webstore/detail/%E3%83%96%E3%82%AF%E3%83%AD%E3%82%B0/iafmiijigjljooijdlehfidpoajlcpjd?hl=ja
[91]: http://blog.kentarok.org/entry/2013/09/09/221958

[^01]: やり方<del>丸パクリ</del>が参考になりました。ありがとうございます。

[^02]: データ編集後にいちいち保存しなくても、よしなにやってくれるのはマジ便利

[^03]: 検索等の問い合わせ時にパラメータを URL で渡すための仕組みで、URL の `?` 以降がそれに当たる。複数指定でき、その場合は `&` で区切る。
