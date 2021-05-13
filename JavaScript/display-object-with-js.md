# オブジェクトの内容を表示する方法

## detail

JavaScriptで、オブジェクトの内容を表示する方法について記述する。

```javascript
let hoge = {
  foo: 'eggs',
  bar: 'ham',
  baz: ['spam', 100, 200],
  foobarbaz: {
    'fooFoo': 1,
    'barBar': 2,
    'bazBaz': 3
  },
  func: function(){
    console.log('hoge');
  },
  func2: ()=>{
    console.log('hoge2');
  }
};

console.log(hoge);
```

上記のようなオブジェクトを定義し、その内容をコンソールへ出力したい場合は、単純に`console.log()`で表示するのが手っ取り早い。

```console
{
    "foo": "eggs",
    "bar": "ham",
    "baz": [
        "spam",
        100,
        200
    ],
    "foobarbaz": {
        "fooFoo": 1,
        "barBar": 2,
        "bazBaz": 3
    }
}
```

ただ、`console.log()`に出力する方法は関数オブジェクトを出力できないため、上記のようにすべてのメンバについて出力できるわけではない。

そこで別な方法を使ってみる。

```javascript
for (let i in hoge){
  console.log(i + ': ' + JSON.stringify(hoge[i]));
}
```

`JSON.stringify()`を使ってみる。

```console
"foo: 'eggs'"
"bar: 'ham'"
"baz: ['spam',100,200]"
"foobarbaz: {'fooFoo':1,'barBar':2,'bazBaz':3}"
"func: undefined"
"func2: undefined"
```

関数を定義しているはずのメンバは、表示こそされてものの`undefined`になってしまった。

```javascript
for (let i in hoge){
  console.log(i + ': ' + hoge[i]);
}
```

`JSON.stringify()`を取ったらどうなるか。

```console
"foo: eggs"
"bar: ham"
"baz: spam,100,200"
"foobarbaz: [object Object]"
"func: function () {
        console.log('hoge');
    }"
"func2: () => {
        console.log('hoge2');
    }"
```

関数の内容は表示されたものの、今度はオブジェクトのメンバが`[object Object]`となってしまい、内容がわからなくなってしまった。

関数のメンバの内容を表示しつつ、オブジェクトの内容も表示できる方法はないものだろうか。なお、今回はFirefoxとCodepenの環境で検証しているため、他の環境ではまた異なる挙動をする可能性がある。

## reference

1. [JSON.stringify()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
1. [【JavaScript】オブジェクトの中身を知る方法4種](https://oki2a24.com/2015/09/19/how-to-know-object-inside-in-javascript/)
