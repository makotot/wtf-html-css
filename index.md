---
layout: default
---

### コンテンツ

- [doctypeを宣言する](#doctype)
- [ボックスモデルの計算](#box-model-math)
- [Rem の単位とモバイルSafari](#rems-mobile-safari)
- [float が最初](#floats-first)
- [float と clear](#floats-clearing)
- [float と計算された高さ](#floats-computed-height)
- [float を使用した要素はブロックレベル](#floats-block-level)
- [垂直方向に隣接するマージンはしばしば崩壊する](#vertical-margins-collapse)
- [table の行のスタイル](#styling-table-rows)
- [Firefox と `<input>`ボタン](#buttons-firefox)
- [Firefox のボタンの内側のアウトライン](#buttons-firefox-outline)
- [`<button>` に `type` を常に与える](#buttons-type)
- [Internet Explorer のセレクタの制限](#ie-selector-limit)
- [position の説明](#position-explained)
- [position と width](#position-width)
- [固定された position と transform](#position-transforms)


<a name="doctype"></a>
### doctype を宣言する
常に doctype を宣言しましょう。 私はシンプルな HTML5 doctype を推奨します。:

```html
<!DOCTYPE html>
```

不正な形式のtable、inputなどと一緒に[doctype を抜かすことが問題を引き起こし](http://quirks.spec.whatwg.org)、ページは互換モードでレンダリングされます。


<a name="box-model-math"></a>
### ボックスモデルの計算
`width`が与えられている要素が`padding`か`border-width`を持ったとき、`幅が広く`なります。この問題を避けるには、今では一般的な[`box-sizing: border-box;`リセット](http://www.paulirish.com/2012/box-sizing-border-box-ftw/)を使用することです。


<a name="rems-mobile-safari"></a>
### Rem の単位とモバイルSafari
モバイルSafariが全てのプロパティの値で`rem`の使用をサポートしていますが、メディアクエリのサイズ指定で`rem`が使われたときに駄目になるようで、ページのテキストが違うサイズで無限に点滅します。

今のところ、`rem`の代わりに`em`を使いましょう。

```css
html {
  font-size: 16px;
}

/* モバイルSafariで点滅するバグを起こします */
@media (min-width: 40rem) {
  html {
    font-size: 20px;
  }
}

/* モバイルSafariで問題なく機能します */
@media (min-width: 40em) {
  html {
    font-size: 20px;
  }
}
```

**助けが必要です!** *もし、これに関してAppleかWebkitのバグレポートへのリンクを知っていたら, それを追加できたらと思っています。私は、Safariのデスクトップではなくモバイルだけに対してこの報告がされているのがどこでなのか、明確に分かりません。*


<a name="floats-first"></a>
### float が最初
floatの要素はドキュメントの順番において常に最初にくるべきです。floatの要素は囲う何かを必要とします。そうしないと内容に次いで表示されるのではなく、下に入り込んでしまいます。

```html
<div class="parent">
  <div class="float">Float</div>
  <div class="content">
    <!-- ... -->
  </div>
</div>
```


<a name="floats-clearing"></a>
### float と clear
もし`float`が使用されていれば、*おそらく*clearする必要があります。`float`を使用した要素につづくどんな内容も、クリアされるまでその要素に巻きつきます。floatを解除するには、以下のいずれかのテクニックを使いましょう。

別クラスでfloatを解除するには[the micro clearfix](http://nicolasgallagher.com/micro-clearfix-hack/)を使いましょう。

```css
.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}
```

あるいは、親要素で`overflow`を`auto`か`hidden`で使いましょう。

```css
.parent {
  overflow: auto; /* clearfix */
}
.other-parent {
  overflow: hidden; /* clearfix */
}
```


一般的には親要素の中に配置された要素で、`overflow`が意図しない副作用を引き起こすことに気をつけてください。

**ヒント!** *他の理由のために使用されることがあるので、floatを解除するとき`/* clearfix */`のようなコメントを入れておくことで、あなたと共同作業者の未来を幸せにしましょう。*


<a name="floats-computed-height"></a>
### float と計算された高さ
floatを使用した中身だけを持つ親要素は`height: 0;`に計算されます。ブラウザに親要素の高さを算出させるにはclearfixを与えましょう。


<a name="floats-block-level"></a>
### float を使用した要素はブロックレベル
`float`を使った要素は自動的に`display: block;`になります。`float`は`display`を上書きするし、必要もないので、両方を指定するのはやめましょう。

```css
.element {
  float: left;
  display: block; /* 必要ない */
}
```

**面白い事実:** *数年前、[マージンが倍になるバグ](http://www.positioniseverything.net/explorer/doubled-margin.html)をIE6で避けるために、適切にfloatを働かせるには`display: inline;`を使う必要がありました。ですが、当時からずいぶん時間が経ちました。*


<a name="vertical-margins-collapse"></a>
### 隣接する垂直方向のマージンは崩壊する
隣接する要素（前後）のtopとbottomのマージンは様々な状況で崩壊しますが、決してfloatやabsoluteで配置した要素によるものではありません。詳しくは、[MDNの記事を読む](https://developer.mozilla.org/en-US/docs/Web/CSS/margin_collapsing)かCSS2の仕様の[マージンの崩壊について](http://www.w3.org/TR/CSS2/box.html#collapsing-margins)を参照してください。

水平方向に隣接したマージンは**決して崩壊しません**。


<a name="styling-table-rows"></a>
### table の行のスタイル
tableの行`<tr>`は、親の`<table>`に対して`border-collapse: collapse;`を与えない限り`border`を認識しません。さらに、`<tr>`とその子の`<td>`か`<th>`が*同じ*`border-width`を持っていれば、行にボーダーが適応されているのを見られません。[例としてこのJSBinのリンクを見てください](http://jsbin.com/yabek/2/)。


<a name="buttons-firefox"></a>
### Firefox と `<input>`ボタン

理由はよく分かりませんが、Firefoxは`<input>`のsubmitとbuttonに対してユーザー定義のCSSで上書きできない`line-height`を当てます。これに対処するのに２つの方法があります:

1. `<button>`要素にする
2. `line-height`をボタンに使わない

1つめの方法を取るのであれば（どっちみち`<button>`は素晴らしいので私はこっちを推奨します）、知っておくべきことは以下のようなことです:

```html
<!-- あまり良くありません -->
<input type="submit" value="Save changes">
<input type="button" value="Cancel">

<!-- いつでも最高です -->
<button type="submit">Save changes</button>
<button type="button">Cancel</button>
```

2つめの方法を取るのであれば、`line-height`を与えないこととボタンのテキストを垂直方向に中央寄せするのに`padding`*だけ*を使用することです。Firefoxで実際の問題と回避方法を[このJSBinの例で見てください](http://jsbin.com/yabek/4/)。
Should you wish to go the second route, just don't set a `line-height` and use *only* `padding` to vertically align button text. [View this JS Bin example](http://jsbin.com/yabek/4/) in Firefox to see the original problem and the workaround.

**グッドニュース!** *[これについての修正](https://bugzilla.mozilla.org/show_bug.cgi?id=697451#c43)がFirefox 30に含まれているようです。これは私たち自身の未来にとっていいニュースですが、古いバージョンでは修正されてないことに気をつけてください。*


<a name="buttons-firefox-outline"></a>
### Firefox のボタンの内側のアウトライン

Firefoxは`:focus`時のボタン（`<input>`と`<button>`）に[内側のアウトラインを追加](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#Notes)しました。明らかにアクセシビリティのためですが、その配置は奇妙に見えます。上書きするのにこのCSSを使ってください:

```css
input::-moz-focus-inner,
button::-moz-focus-inner {
  padding: 0;
  border: 0;
}
```

この修正を、前のセクションでも紹介したのと同じ[JSBinの例](http://jsbin.com/yabek/4/)で見ることができます。

**ヒント!** *ボタン、リンク、inputに対してfocusの状態を持つようにさせましょう。タブを使うユーザーと視覚障害を持つユーザー両方にとって、アクセシビリティのためにアフォーダンスを提供することは最高のことです。*


<a name="buttons-type"></a>
### `<button>`に`type`を常に与える
初期値は`submit`で、フォームの中のどのボタンもフォームを送信できてしまいます。フォームを送信しないボタン全てに`type="button"`を与えて、送信するボタンのみ`type="submit"`を与えましょう。

```html
<button type="submit">Save changes</button>
<button type="button">Cancel</button>
```

フォームの中にないけれどもアクションのために`<button>`が必要なときは、`type="button"`を使用しましょう。

```html
<button class="dismiss" type="button">x</button>
```

**面白い事実:** *明らかにIE7は`<button>`の`value`属性を適切にサポートしていません。属性値を読み取る代わりに、innerHTML（`<button>`の開始タグから終了タグの間の内容）から引っ張ってきています。ですが、2つの理由からあまり注目するべきことではないと思います: IE7の利用率は低下していってますし、`<button>`に`value`とinnerHTMLを両方与えることは、むしろ珍しいことなので。*


<a name="ie-selector-limit"></a>
### Internet Explorer のセレクタの上限
IEの9とそれ以下のバージョンは、1つのスタイルシートで4,096のセレクタが上限になっています。また、1ページに対してスタイルシートと`<style></style>`を合わせて31の上限もあります。このブラウザでは、この上限を超えたものは全て無視されます。CSSを分割するか、リファクタリングしましょう。私は後者を推奨します。

助けになる注釈として、どうやってブラウザがセレクタを数えるかを以下に示します:

```css
/* 1つのセレクタ */
.element { }

/* 2つ以上のセレクタ */
.element,
.other-element { }

/* 3つ以上のセレクタ */
input[type="text"],
.form-control,
.form-group > input { }
```


<a name="position-explained"></a>
### position の説明
`position: fixed;`を使用した要素はブラウザのビューポートに相対して配置されます。`position: absolute;`を使用した要素は、`static`以外（`relative`、`absolute`、`fixed`）で配置されている最も近い親要素に相対して配置されます。
Elements with `position: fixed;` are placed relative to the browser viewport. Elements with `position: absolute;` are placed relative to their closest parent with a position other than `static` (e.g., `relative`, `absolute`, or `fixed`).


<a name="position-width"></a>
### position と width

`position: [absolute|fixed];`と`left`と`right`をもつ要素に`width: 100%;`を与えないでください。`width: 100%;`を使うことは、`left: 0;`と`right: 0;`を合わせて使うことと同じです。どちらか一方を使って、両方を使わないでください。


<a name="position-transforms"></a>
### 固定された position と transform
親要素が`transform`をもつとき、ブラウザは`position: fixed;`を壊します。transformを使うことによって、新しい包含ブロックを作成され、事実上親要素に`position: relative;`を持たせ、固定された要素が`position: absolute;`として振舞うようになります。

[デモを見て](http://jsbin.com/yabek/1/)、[エリック・メイヤー氏によるこの問題についての投稿](http://meyerweb.com/eric/thoughts/2011/09/12/un-fixing-fixed-elements-with-css-transforms/)を読んでください。
