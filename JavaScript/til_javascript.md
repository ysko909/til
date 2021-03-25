# TIL - JavaScript

JSに関することはここに書く。

## JavaScriptでのアップロード機能の実装

### detail

- JavaScriptでローカルの画像をアップロードし、canvas上に表示するプログラムを作成。HTMLとJavaScriptなので、PlayCode上にて仮実装。
- [参考元URL](https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html)では、画像ファイルであれば受け付けていたが、ここではPNGのみを対象とした。`type.match()`の対象を、`image/png`とするとPNGに限定できる。
- もともとの仕様では画像がアップロードされた場合のみcanvasの更新を行っていたため、対象外のファイルがアップロードされた場合は、過去にアップロードされたデータや子要素が画面に残り続ける。それを解消するため、PNG以外がアップロードされた場合は一度canvasを削除する仕様とした。また、同様にダウンロード用URLが存在する場合は子要素から削除する。
- PlayCodeは特定のボタンを押すと、ソースのフォーマットを行ってくれる。その際、アロー関数に引数がある場合は**かっこを外す**（引数がない場合はかっこを省略できないので、そのまま残る）。「挙動は一緒なんだから、文字は少ない方がいいでしょ」という判断なのかもしれないが、個人的にはまだアロー関数に慣れてないので、あんまり省略されると混乱する。
- アロー関数ではかっこだけでなく、1行で記述する場合は波かっこまで省略可能なので、ぱっと見の可読性に難があって割と混乱しがち。
- どっちがいいかはともかくとして、「どっちでも読める」程度には慣れたい。

### reference

1. [File APIとCanvasでローカルの画像をアップロード→加工→ダウンロードする](https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html)
1. [ソースのgist](https://gist.github.com/ysko909/0f14a71c5604ded658e3a4f8fb837e6d)

## 英字の文字列を大文字または小文字に変換する

### detail

英字の文字列は`toUpperCase()`を使って大文字に、`toLowerCase()`を使って小文字に変換できる。

```javascript
const $=arg=>{
  console.log(arg);
};

let hoge = 'hOgE';

$(hoge.toUpperCase());
$(hoge.toLowerCase());

$(hoge.slice(0, 1).toUpperCase() + hoge.slice(1).toLowerCase());
```

`toUpperCase()`と`toLowerCase()`、さらに`slice()`を組み合わせることで「頭文字だけを大文字に、他を小文字に変換する」なんてこともできる。

### reference

1. [大文字、小文字に変換する方法](https://lab.syncer.jp/Web/JavaScript/Snippet/45/)

## オブジェクトの内容を表示する方法

### detail

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

### reference

1. [JSON.stringify()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
1. [【JavaScript】オブジェクトの中身を知る方法4種](https://oki2a24.com/2015/09/19/how-to-know-object-inside-in-javascript/)
