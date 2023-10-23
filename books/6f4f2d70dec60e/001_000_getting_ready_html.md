---
title: "JavaScriptであそぶ準備"
---

JavaScriptは、ウェブページで動きます。
なので、その準備をします。

## プログラムを動かすファイルたちを作る

プログラムを動かすためのフォルダを一つ作ってください。
（今回は、`dir`という名前にしました）

そのなかに、`index.html`、`style.css`、`script.js`という3つのファイルを作ってください。

- dir/
    - index.html
    - style.css
    - script.js
    
こんな階層になると思います。

### index.html
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

### script.js
```js
console.log('Hello JavaScript!');
```

### style.css
```css
```
※ からっぽ

### 実行してみる

作った `index.html` を開いてみてください。
真っ白な画面が出てくると思います。

`F12`を押すと、何やらDevToolsなるものが出てきます。

`Console` `コンソール` って書いてあるタブを開くと、`Hello JavaScript!` って書いてあると思います。
そうしたら、成功です。

### プログラムを書き換えてみる

`script.js` がJavaScript用のファイルなので、それを書き換えて遊びます。

試しに、中身を次に書き換えてみましょう。
```js
console.log('I am JavaScript!');
```

`index.html` を開いたブラウザを更新すると、コンソールに書いてあった文字が変わっているはずです。
