# ランダムな数値を取得する

## detail

`Math.random()`使えばいいじゃん。

いやまぁそうなんだけど、小数じゃなくて整数が欲しいとか重複する数値があると困るとか、そういうケースについてもちょっと考えてみる。

### 基本形

まずは`Math.random()`の基本的な使い方。

```javascript
let hoge = Math.random();
```

上記を実行すると、0から1の間で任意の小数を取得する。

```javascript
let hoge = Math.floor(Math.random());
```

上記を実行すると、引数が正の小数である場合は小数を切り捨てた値を返す。負の場合は切り捨てというより、切り下げになるかな（`Math.floor(-5.1)`は-6を返す）。

### 指定した区間の値を取得する

今度は、「10から50の間でランダムな数値が欲しい」というケースに対応する方法。

```javascript
const getRandomArbitrary = (min, max) => Math.random() * (max - min) + min;
```

まず小数では上記の関数で取得可能。

```javascript
const getRandomInt = (min, max) => {
  min = Math.ceil(min);
  max = Math.floor(max);
  return Math.floor(Math.random() * (max - min) + min);
}
```

整数の場合は基本的には先ほどと同様だが、`Math.floor()`だけでなく`Math.ceil()`も一緒に利用する。`Math.ceil()`は切り上げ処理を行う。なお、上記の処理で返ってくる値は、**min以上max未満**である点に注意が必要。

```javascript
const getRandomIntInclusive = (min, max) => {
  min = Math.ceil(min);
  max = Math.floor(max);
  return Math.floor(Math.random() * (max - min + 1) + min);
}
```

上記のコードなら**min以上max以下**で指定できる。違いは`max-min`の処理結果に1を足しているところ。

## reference

1. [Math.random()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Math/random)

