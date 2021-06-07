# 処理時間を計測する

## detail

ある任意の処理について、その処理時間を計測したいとする。

```javascript
const start = Date.now();
hoge(); // 計測したい処理
const end = Date.now();

console.log(end - start);
```

上記のコードでも一応計測はできる。ただ、結構大きな問題があって、それが`Date.now()`が**ミリ秒までしか計測できない**こと。スマートフォンに限らず、最近デバイスはそもそもハード設計からして高速であることと、

## reference

1. []()
