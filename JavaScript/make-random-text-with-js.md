# ランダムな文字列を生成する

## detail

パスワードなどには**利用しない**前提で、ランダムな英数字の文字列を生成する。セキュアな用途で利用するなら、乱数生成器を利用する方がいい。

```javascript
const generateRandomStr = length => {
  const c = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
  
  const getRandomInt = (min, max) => {
    min = Math.ceil(min);
    max = Math.floor(max);
    return Math.floor(Math.random() * (max - min) + min);
  }
  
  return [...Array(length)].map(()=>c[getRandomInt(0, c.length)]).join('');
}

// sample
console.log(generateRandomStr(8));

```

定数宣言した文字列`c`の中から、指定した文字列長`length`分ランダムで文字をピックアップして連結する。JSには「min以上max未満の間でランダムに値を生成する」という関数は準備されていない（`Math.random()`は0から1の間で値を返す）ので、自前で実装する必要がある。

## reference

1. [Math.random()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Math/random)
2. [JavaScriptでお手軽にランダム文字列の生成](https://qiita.com/fukasawah/items/db7f0405564bdc37820e)
