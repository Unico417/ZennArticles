---
title: "【JavaScript】乱数、ランダムな文字列を生成する"
emoji: "🔠"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "初心者向け"]
published: true
---

## ランダムな値を取得

JavaScriptには、`Math.random()`という関数があります。
これは、0～0.9999999... の値を取ります。
１は含みません。

その値に最大値にしたい値を掛けると、最大値までのランダムな値が取れます。

```js
// 最大値（1000と仮定）
const max = 1000;

// 乱数 （0 ～ 999.9999）
const num = Math.random() * max;
```

### 最大値を取ることがない問題

上記の場合、`Math.random()`が１になることはないので、値が最大値になることはありません。

> `Math.random()`が 0 のとき: 
> 0 * 1000 = 0

> `Math.random()`が 0.9999999 のとき: 
> 0.9999999 * 1000 = 999.99

このままでは最大値を取らない欠陥乱数なので、
最大値に設定した値に、１を足してあげます。

> `Math.random()`が 0 のとき: 
> 0 * 1001 = 0

> `Math.random()`が 0.9999999 のとき: 
> 0.9999999 * 1001 = 1000.998

```js
// 最大値
const max = 1000;

// 乱数 （0 ～ 100.998）
const num = Math.random() * (max + 1);
```

### 返す値を整数にしてあげる

実数を`Math.trunc()`の引数に渡してあげることで、小数点以下を切り落とした整数が取れます。

```js
// 最大値
const max = 1000;

// 乱数 （0 ～ 100.998）
const num = Math.random() * (max + 1);

// 整数の乱数 （0 ～ 100）
const numInt = Math.trunc(num);
```

### 最大値を自由に設定できるようにする

サンプルで最大値を1000にしていましたが、自由に設定できる必要があります。
そのために、関数にしてあげましょう。

```js
function randomInteger (max) {

    const num = Math.random() * (max + 1);
    const numInt = Math.trunc(num);

    return numInt;
}

// 最大値を引数にして、渡してあげると
// 0 ～ 最大値 の値を取る乱数がもらえる関数
randomInteger(1000);
```

### エラー処理を追加する

関数の引数には、何が入ってくるかわからないので、エラーチェックをしましょう。

`max`に許されるのは、以下の条件を満たすものですね。

- 数値 （Number）
- 整数
- 1以上 （1 <= max）

**数値**のチェックをする場合には、`typeof max === 'number'`とすれば分かります。
ですが、今回はもっと便利なものを使います。

`Number.isInteger()`を遣えば、**数値であり整数である**場合に`true`が返ってきます。

```js diff
function randomInteger (max) {

+   if (
+       !Number.isInteger(max)  // max が 数値・整数でない
+       || max < 1              // or 1未満 
+   ) {
+       throw new Error(`最大値が不正です。 ${max}`);  // エラー
+   }

    const num = Math.random() * (max + 1);
    const numInt = Math.trunc(num);

    return numInt;
}
```

## ランダムな値を範囲で取得

ランダムな値を取る時、下限がほしいこともあるかもしれません。
~~あるのかな、知りませんけど。~~

そのために、下限を設定できるようにしてあげます。

1. 最大値から、下限を引いた分までの乱数を取る
2. 下限の分だけ足す

これでできそうですね。

```js
// 最大値・最小値
const min = 5;
const max = 20;
// 最大値から最小値を引いた値
const diff = max - min;

// 0 ～ 15 の乱数 （前項で作った関数）
const numInt = randomInteger(diff);
// 5 ～ 20 の乱数
const numRangeInt = min + numInt;
```

### 関数化

処理を関数にしてあげて、自由な値で使えるようにしてあげます。

```js
function randomRangeInteger (min, max) {

    const diff = max - min;
    const numInt = randomInteger(diff);
    const numRangeInt = min + numInt;

    return numRangeInt;
}
```

### エラーチェック

エラーチェックも入れましょう。
やっていることは、以前やったことと同じです。

```js diff
function randomRangeInteger (min, max) {

+   if (!Number.isInteger(min) || min < 1) {
+       throw new Error(`最小値が不正です。 ${min}`);
+   }

+   if (!Number.isInteger(max) || max < 1) {
+       throw new Error(`最大値が不正です。 ${max}`);
+   }

    const diff = max - min;
    const numInt = randomInteger(diff);
    const numRangeInt = min + numInt;

    return numRangeInt;
}
```

おっと、`min` が `max` を超えてはいけませんね。

```js diff
function randomRangeInteger (min, max) {

    if (!Number.isInteger(min) || min < 1) {
        throw new Error(`最小値が不正です。 ${min}`);
    }

    if (!Number.isInteger(max) || max < 1) {
        throw new Error(`最大値が不正です。 ${max}`);
    }

+   if (max < min) {
+       throw new Error(
+           `最小値(${min})が 最大値(${min})を 超えています。 `);
+   }

    const diff = max - min;
    const numInt = randomInteger(diff);
    const numRangeInt = min + numInt;

    return numRangeInt;
}
```

## ランダムな文字を生成

次はランダムな文字を生成してみます。
出てくる文字は、`0-9`, `A-Z`, `a-z`のいずれかです。

### 出現する文字を作成

出現する文字を `,` で区切った文字列を用意します。

```js
const spells
    = '0,1,2,3,4,5,6,7,8,9,'
    + 'a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,'
    + 'A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z';
```

### 出現する文字のリストを作成

文字列の `.split()` メソッドを実行し、区切りの文字を引数に渡すことで、
その文字の出現箇所で区切ったArrayにしてくれます。

今回の文字列は`spells`、区切り文字は `,` なので、以下のようになります。

```js
const spells
    = '0,1,2,3,4,5,6,7,8,9,'
    + 'a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,'
    + 'A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z';

const spellArray = spells.split(',');
// spellArray = ['0', '1', '2', ... 'a', 'b', 'c', ... 'X', 'Y', 'Z']
```

### 出現する文字の数までの乱数を取得

Arrayの要素数（長さ）は、`.length` で得ることができます。
その長さまでのランダムな値を一つ取得します。

:::message
`[1, 2, 3]` の要素数は、`3`です。

ただし、要素を指定する値は __0 ～ 要素数-1__ なので、
最大値は`-1`する必要があります。
:::

```js
const spells
    = '0,1,2,3,4,5,6,7,8,9,'
    + 'a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,'
    + 'A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z';

const spellArray = spells.split(',');
// spellArray = ['0', '1', '2', ... 'a', 'b', 'c', ... 'X', 'Y', 'Z']

// Arrayの要素数
const spellLength = spellArray.length;

// Arrayの要素数-1 を最大値とした乱数
const index = randomInteger(spellLength - 1);
```

### 乱数 番目の文字を得る

文字の行列から、乱数の位置の文字を得ることでランダムな一文字を得ることができます。

```js
const spells
    = '0,1,2,3,4,5,6,7,8,9,'
    + 'a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,'
    + 'A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z';

const spellArray = spells.split(',');
// spellArray = ['0', '1', '2', ... 'a', 'b', 'c', ... 'X', 'Y', 'Z']

// Arrayの要素数
const spellLength = spellArray.length;

// Arrayの要素数-1 を最大値とした乱数
const index = randomInteger(spellLength - 1);

const spell = spellArray[index];
```

### 関数化

これは引数がなく１文字生成するだけなので、エラーチェックは必要ありません。

```js
function randomSpell () {
    const spells
        = '0,1,2,3,4,5,6,7,8,9,'
        + 'a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,'
        + 'A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z';

    const spellArray = spells.split(',');
    // spellArray = ['0', '1', '2', ... 'a', 'b', 'c', ... 'X', 'Y', 'Z']

    // Arrayの要素数
    const spellLength = spellArray.length;

    // Arrayの要素数-1 を最大値とした乱数
    const index = randomInteger(spellLength - 1);

    // 乱数 番目の文字を取得
    const spell = spellArray[index];

    return spell;
}
```

## n文字の乱数を得る

n文字の乱数を得る方法を考えましょう。
これが関数にできると、アプリケーションの簡易的なUIDを生成することができます。

:::message
UIDとは、複数の要素があったときに、それらを一意に識別するために付与されるものです。
イメージ的には、ウェブサイトのアカウントのIDに似たものです。
:::

といっても、先ほど作った `randomSpell()`関数を n回実行するだけです。
実行する回数を引数から受け取り、for文でその回数発行してあげましょう。

```js
function randomString (length) {
    let string = '';

    for (let i = 1; i <= length; i++) {
        string += randomSpell();
    }

    return string;
}
```

エラー対策をして、完成です。

```js diff
function randomString (length) {
    
+   if (!Number.isInteger(length) || length < 1) {
+       throw new Error(`文字の長さが不正です。 ${length}`);
+   }
    
    let string = '';

    for (let i = 1; i <= length; i++) {
        string += randomSpell();
    }

    return string;
}
```

これでランダムな文字列が作り放題です。
