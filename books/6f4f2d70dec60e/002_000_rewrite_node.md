---
title: "JavaScriptで画面の文字を変える"
---

ファイルを作った時、`.html`, `.css`, `.js` と3つのファイルを作りましたね。
実は、あの３ファイルは三つ子のように関係の深いファイルなんです。

- HTMLは、ページの**中身**を決めます。
- CSSは、中身の色や背景色など**デザイン**を決めます。
- JSは、ページの動きなどを制御します。

この３つのファイルが連携することで、様々なウェブページが出来上がります。
なので申し訳ないのですが、JavaScriptの勉強はJavaScriptだけの勉強で収まりません。

# 軽くHTMLのおさらい

HTMLファイルを見てみましょう。
今後のために、解説付きで

```html
<html>

<head>
<link rel="stylesheet" href="style.css">
</head>

<body>
<script src="script.js"></script>
</body>

</html>
```

よく見ると、`<html>` と `</html>` のように、同じ名前がワンペアで存在しています。

スラッシュが付いていない方が開始、付いている方が終了を表します。

- `<html> ~ </html>` は、「ここから ここまで HTMLですよ」
- `<head> ~ </head>` は、「ここから ここまで このページの情報ですよ」
- `<body> ~ </body>` は、「ここから ここまで このページの表示内容ですよ」

といった具合です。

- `<link rel="stylesheet" href="style.css">` で、`style.css` と連携しています。
- `<script src="script.js"></script>`で、`script.js` と連携しています。

# HTMLに一行追加してみる

HTMLファイルに、次の一行を追加してみます。

```diff html
 <html>
 
 <head>
 <link rel="stylesheet" href="style.css">
 </head>
 
 <body>
+<p>Hello JavaScript!</p>
 <script src="script.js"></script>
 </body>
 
 </html>
```

緑色の部分が追加されました。
`<p>` タグは、『ここはひとつの文節だよ』って意味です。

これを保存して、ブラウザを更新すると、真っ白だったページに「Hello JavaScript!」という文字が出てきたと思います。

# JSからHTMLを書き換えてみる

ではさっそく、JSからHTMLを書き換えてみましょう。
まずはHTMLの準備をします。

```diff html
 <html>
 
 <head>
 <link rel="stylesheet" href="style.css">
 </head>
 
 <body>
+<p id="target">Hello JavaScript!</p>
 <script src="script.js"></script>
 </body>
 
 </html>
```

緑色の行に、`id="target"`という文字が追加されました。
`<p>` に、`target` という名札(id)をつけてあげました。
※ idは、このページ内に同じ名前の要素が一つでなければなりません。

このidがあることで、圧倒的にJavaScript空の制御が楽になります。

次は、JavaScriptを書き換えましょう。

```js
let target = document.getElementById('target');
target.textContent = 'Hello World!';
```

これを保存したら、ブラウザを更新してみましょう。

なんということでしょう。
HTMLでは「Hello JavaScript!」という文字が書いてあるのに、画面には「Hello World!」と出てきています。
不思議ですね。

```js
document.getElementById('target');
```

ここで突然出てきた`document`。
これはHTML全体を表します。

それに続けて、`.getElementById('target')`で、`target`というidを持った要素を探してくれます。
結果的に、`<p id="target">Hello JavaScript!</p>`が取得できます。
`<p>`に、idを付けた理由がこれです。

それを、変数`target` に入れて、覚えてもらいます。

次の謎は、`target.textContent = 'Hello World!'` ですね。
その`target` に入った `<p>`の中身が`.textContent` に入っています。
そこに、新しく`Hello World!` という文字を上書きしています。
なので、画面に表示されるのが「Hello World!」になります。

はじめてだと、頭がおかしくなりそうですね。

