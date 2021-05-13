# 2つの配列から1つのオブジェクトを作成する

## detail

同じ要素数を持つ配列AとBの2つを使って、配列Aの要素をキーに、配列Bの要素をプロパティに設定したオブジェクトを作る。

単純に配列同士を結合できないし、結合できたとしてもそれは配列でありオブジェクトではない。では、オブジェクトとして出力するためにはどうするかというと、`Object.fromEntries()`を利用する。

```javascript

let hoge = [10, 11, 12];
let fuga = ['foo', 'bar', 'baz'];

let piyo = [];
hoge.forEach((val, idx)=>{
    piyo.push([val, fuga[idx]]);
});

let foo = Object.fromEntries(piyo);

console.log(piyo);
console.log(foo);
```

配列`hoge`と`fuga`は一度要素をそれぞれから取り出して、新しい配列`piyo`に値をセットしている。これは、`Object.fromEntries()`が「2つの要素を持つ配列」を1つの「キー：プロパティ」として解釈するため、作りたいオブジェクトの形に合うよう配列を整形する必要があるから。

```console
(3) [[10,"foo"], [11,"bar"], [12,"baz"]]

{
10:"foo",
11:"bar",
12:"baz"
}
```

上記のように、`[[key, value], [key, value], [key, value], ...]`という形に整形してから、`Object.fromEntries()`に渡す。このような順番で処理することで、オブジェクトが配列から作成できる。

## reference

1. [Object.fromEntries()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries)
