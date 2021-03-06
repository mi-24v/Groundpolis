# Misskey Flavored Markdown

Misskey Flavored Markdown 記法を使うことで、ノートを装飾できます。この装飾記法は、ユーザープロフィールにも一部使えます。

この記法は Groundpolis ではない Misskey インスタンスでも共通して使えます。

## 基本

`**` で文字を囲むと、 **太字** になります。

例: `**ぽぺ**` _ぽぺ_

`<i>`と`</i>` で文字を囲むと、 _斜体_ になります。

例: `<i>ぽぺ</i>` _ぽぺ_

`~~` で文字を囲むと、 ~~文字を取り消します~~。

例: `~~ぽぺ~~` ~~ぽぺ~~

`<small>`と`</small>` で文字を囲むと、文字が小さく表示されます。

`<center>`と`</center>` で文字を囲むと、文字が中央揃えされます。

`<flip>`と`</flip>`で文字を囲むと、文字が反転します。

## 動き

`***`で文字を囲むと、とても大きく表示されます。

`<motion>`と`</motion>` で文字を囲むと、文字が横に伸びるようなアニメーションを付与できます。
`(((`と`)))`で囲っても同じです。

`<spin>`と`</spin>` で文字を囲むと、文字が回転します。`<spin left>` とすると左回転、 `<spin alternate>` とすると反復します。

`<jump>`と`</jump>` で文字を囲むと、文字がジャンプします。

## コード

<code>```</code> で囲まれた箇所は、ソースコードを記述するための整形された状態で表示されます。改行も可能です。

改行してほしくない場合は ` 単体で似たような状態になります。

## 引用

`>` から始まる行は引用ブロックとして表現されます。

## タイトル

`[]` `【】` などで囲んだ文字列はタイトルとして表示されます。

## リンク系

http https の URL は貼るだけでリンクとして表示されますが、日本語を含むリンクはうまく解析できないことがあります。その場合は URL を `<`と`>`で区切ります。

また、 `[Text](URL)` 記法を使うとリンクを作成できます。Text には表示する文字列、URLには飛んで欲しいURLを指定します。

`?[Text](URL)` のように、先頭に ? をつけると、ノートの下にサマリーが表示されなくなる隠しリンクになります。

## 検索ボックス

`Text 検索` `Text Search` `Text [検索]` `Text [Search]` というように書くと、検索ボックスが現れます。

## 数式

LaTeX 記法に精通しているなら、Misskey で数式を思いのままに操ることができます。

`\(` `\)` で LaTeX 記法を囲むことで、数式を表示します。ブロック形式にしたい場合は、代わりに `\[` `\]` で囲みます。

## Groundpolis Flavored MFM

Groundpolis は MFM 記法をいくつか拡張します。これは通称 GPMFM と呼ばれますが、他の Misskey インスタンスでは正しく表示できない可能性があるため、ご注意ください。

### 基本

`<right>`と`</right>` で文字を囲むと、文字が右揃えされます。

`<sup>`と`</sup>` で文字を囲むと、上付き文字になります。

`<sub>`と`</sub>` で文字を囲むと、下付き文字になります。

### 動き

`****`で文字を囲むと、もっともっと大きく表示されます。

`<vflip>`と`</vflip>`で文字を囲むと、文字が縦に反転します。

`<xspin>`と`</xspin>`、`<yspin>`と`</yspin>`はそれぞれX軸方向、Y軸方向に文字を回転します。 `<spin>` と同じく、 `alternate` `left` を指定できます。

`<blink>`と`</blink>`で文字を囲むと、文字が点滅します。

`<marquee>`と`</marquee>`で文字を囲むと、文字が右から左に流れます。
