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

## 関数の引数に再代入することの可否について

### detail

Airbnbのコーディング規約には、関数の引数へ値を再代入することは禁止されているらしい。

> 7.13 パラメータを再割り当てしない。eslint: no-param-reassign

```javascript
const hoge = foo => {
  foo += 1;
  return foo;
}

hoge(1);
```

上記のように、`foo`に再代入するのはダメということになる。

ESLintなどでは、「no-param-reassign」という名前で[ルール](https://eslint.org/docs/rules/no-param-reassign)が存在している。実際、ESLintを体験できる[デモサイト](https://eslint.org/demo)でこのルールを有効にした状態で上記のコードを記述すると、再代入している部分でエラーメッセージが表示される。ただ、このルールはデフォルトでOFFになっている。そこまで優先度は高くないのだろうか。プロジェクトの思想や規約に多少左右されることではあるかもしれないが。

```javascript
const hoge = (foo, bar) => {
  foo = 9;
  bar.push(4);
  console.log(foo.toString() + ' : ' + bar.toString());
}

let n = 1;
let a = [0, 1, 2, 3];

console.log(n.toString() + ' : ' + a.toString());
hoge(n, a);
console.log(n.toString() + ' : ' + a.toString());

```

個人的には、上記のような再代入を行うコードが危ないと思っている。

```console
1 : 0,1,2,3
9 : 0,1,2,3,4
1 : 0,1,2,3,4
```

実行してみると、変数`n`と`a`で比較したとき関数`hoge`を実行する前後で`n`は変化しないものの、配列である`a`は内容が変化してしまっている。関数内で引数に対し操作を行ってしまったため、もともと変数に格納されていた値と乖離してしまうパターンだ。ただ、これは再代入処理が悪いというよりミュータブルとイミュータブルを適切に処理していないために起きている症状なので、再代入の是非を問うケースでは少々例えが悪い気がする。

まぁ、このようなコードを書いてしまい、オブジェクトを書き換えてバグの発生原因を作ってしまうことが危惧されるくらいのコードを書く可能性があるなら、ルールで禁止してしまえということも1つの解決策かもしれない。

### reference

1. [7.13 Never reassign parameters. eslint: no-param-reassign](https://mitsuruog.github.io/javascript-style-guide/#functions--reassign-params)
2. [JavaScriptの仮引数に再代入することの是非](https://teratail.com/questions/173485)
3. [「Airbnb JavaScript Style Guide」の引数への再代入禁止ルール](https://teratail.com/questions/173503)
4. [Why can a function modify some arguments as perceived by the caller, but not others?](https://stackoverflow.com/questions/575196/why-can-a-function-modify-some-arguments-as-perceived-by-the-caller-but-not-oth)
5. [JavaScriptでイミュータブルなプログラミングをする](https://sbfl.net/blog/2018/09/25/javascript-immutable-programming/)
