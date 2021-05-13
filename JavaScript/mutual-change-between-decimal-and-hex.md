# １０進数と16進数を相互変換する

## detail

10進数と16進数を相互に変換する。

まずは、10進数を16進数に変換する。

```javascript
const changeNum = num => {
  console.log('decimal : ' + num.toString());
  console.log('binary : ' + num.toString(2));
  console.log('octal : ' + num.toString(8));
  console.log('hexadecimal : ' + num.toString(16));
};

changeNum(0);
changeNum(1);
changeNum(2);
changeNum(10);
changeNum(16);
changeNum(255);
```

`number.toString(16)`とすると16進数に変換した文字列を返してくれる。

```console
decimal : 0
binary : 0
octal : 0
hexadecimal : 0
decimal : 1
binary : 1
octal : 1
hexadecimal : 1
decimal : 2
binary : 10
octal : 2
hexadecimal : 2
decimal : 10
binary : 1010
octal : 12
hexadecimal : a
decimal : 16
binary : 10000
octal : 20
hexadecimal : 10
decimal : 255
binary : 11111111
octal : 377
hexadecimal : ff
```

ちなみに返された文字列には`0x`が付与されていないため、必要ならば自分で付け足す。

今度は16進数を10進数に変換する。

```javascript
const changeToDecimal = hex => {
  console.log('hexadecimal : ' + hex);
  console.log('decimal : ' + parseInt(hex, 16).toString());
};

changeToDecimal('0x0');
changeToDecimal('0x1');
changeToDecimal('0xa');
changeToDecimal('0xA');
changeToDecimal('0xff');
changeToDecimal('0xff0000');
```

`parseInt()`関数を使って変換する。第1引数に変換対象を、第2引数には基数を指定する。16進数を10進数に変換する場合、基数は16になる。

```console
hexadecimal : 0x0
decimal : 0
hexadecimal : 0x1
decimal : 1
hexadecimal : 0xa
decimal : 10
hexadecimal : 0xA
decimal : 10
hexadecimal : 0xff
decimal : 255
hexadecimal : 0xff0000
decimal : 16711680
```

基数指定で気づいたと思うが、16進数以外も10進数に変換できる。

たとえば2進数を10進数に変換するケース。

```javascript
const changeToDecimal = bin => {
  console.log('binary : ' + bin);
  console.log('decimal : ' + parseInt(bin, 2).toString());
};

changeToDecimal('0');
changeToDecimal('1');
changeToDecimal('1111');
changeToDecimal('1010');
changeToDecimal('11111111');
```

基数に2を指定することで、2進数から10進数への変換であることを指定可能。

```console
binary : 0
decimal : 0
binary : 1
decimal : 1
binary : 1111
decimal : 15
binary : 1010
decimal : 10
binary : 11111111
decimal : 255
```
問題なく変換できている。

## reference

1. [parseInt](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/parseInt)
2. [toString](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)
3. [英語で16進数はHexadecimalでいいのでしょうか？他のn進数はどう表すのでしょうか？](https://q.hatena.ne.jp/1086764371)
