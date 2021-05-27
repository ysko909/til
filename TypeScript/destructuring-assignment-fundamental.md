# オブジェクトや配列から分割代入する

## detail

```typescript
// オブジェクトの分割代入
let obj1 = {key11: 'value', key12: false, key13: 999};

let {key11, key12, key13} = obj1;

console.log(key11, key12, key13);

// 構造の深いデータ
let obj2 = {key21: {key22: {key23: 1000}}};

let {key21: {key22: {key23}}} = obj2;
let key23Alt = obj2.key21.key22.key23;

console.log(key23);
console.log(key23Alt);

// キー名の変更
let obj3 = {key31: 'foo', key32: 'bar', key33: 'baz'};

let {key31: hoge, key32: fuga, key33: piyo} = obj3;

console.log(hoge, fuga, piyo);

// Restパラメータ
let obj4 = {a41: 'a', a42: 'b', a43: 'c', a44: 'd', a45: 'e'}

let {a41: val1, a42: val2, ...val3} = obj4;
console.log(val1, val2, val3);

// 配列の分割代入
let arr1 = [1, 2, 3, 'foo', 'bar', 'baz'];

let [x, y, z, ...others] = arr1;
console.log(x, y, z, others);

// 構造の深いデータ
let arr2 = [[['a', 1], ['b'], ['c', 2]], ['d', 'e', 'f'], ['d', 'd', ['f', 'g', 'h']]];

let [[a1, a2, a3], [b1, b2, b3], [c1, c2, c3]] = arr2;

console.log(a1, a2, a3);
console.log(b1, b2, b3);
console.log(c1, c2, c3);

```

オブジェクトや配列の要素を、個別に取り出して新しい変数に代入するのが分割代入。

```console
[LOG]: "value",  false,  999 
[LOG]: 1000 
[LOG]: 1000 
[LOG]: "foo",  "bar",  "baz" 
[LOG]: "a",  "b",  {
  "a43": "c",
  "a44": "d",
  "a45": "e"
} 
[LOG]: 1,  2,  3,  ["foo", "bar", "baz"] 
[LOG]: ["a", 1],  ["b"],  ["c", 2] 
[LOG]: "d",  "e",  "f" 
[LOG]: "d",  "d",  ["f", "g", "h"] 
```

オブジェクトから変数に対し分割代入する場合で、任意のプロパティがオプション設定であると`type`宣言されたオブジェクトから分割代入するとき、オプションであるプロパティの**初期値**を決定できる。初期値の設定は、オプションではないプロパティに対しても可能だが、オプションでないプロパティはオブジェクトの宣言時に必ず何かしらの値を格納されるので、分割代入の際に初期値が採用されることがないと思われる。

```typescript
type Obj = {
    foo: number;
    bar: string;
    baz?: string;
}

const obj1: Obj = {foo: 1, bar: 'ham', baz: 'eggs'};

let {foo=2, bar='spam', baz='python'} = obj1;

console.log(foo, bar, baz);

```

上記はすべてのプロパティについて分割代入したケース。

```console
1,  "ham",  "eggs" 
```

オブジェクトで宣言された値が、それぞれの変数に分割代入されたことがわかる。この際、初期値として指定した値は採用されていない。

```typescript
type Obj = {
    foo: number;
    bar: string;
    baz?: string;
}

const obj2: Obj = {foo: 2, bar: 'ham'};

let {foo =3, bar='spam', baz='python'} = obj2;

console.log(foo, bar, baz);
```

上記は、オプションであるプロパティを記述しないでオブジェクトを宣言し、分割代入する例。

```console
2,  "ham",  "python" 
```

オプションであるプロパティ以外は、オブジェクトで宣言された値が変数に対し分割代入されている。オプションであるプロパティは値が指定されなかったが、初期値が採用されている。

## reference

1. [分割代入](https://typescript-jp.gitbook.io/deep-dive/future-javascript/destructuring)
2. [残余引数](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Functions/rest_parameters)
