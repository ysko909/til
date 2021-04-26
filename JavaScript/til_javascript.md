# TIL - JavaScript

JSに関することはここに書く。

## JavaScriptでのアップロード機能の実装

### detail

JavaScriptでローカルの画像をアップロードし、canvas上に表示するプログラムを作成。HTMLとJavaScriptなので、PlayCode上にて仮実装。ソースは[gist](https://gist.github.com/ysko909/0f14a71c5604ded658e3a4f8fb837e6d)にアップした。

[参考元URL](https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html)では、画像ファイルであれば受け付けていたが、ここではPNGのみを対象とした。`type.match()`の対象を、`image/png`とするとPNGに限定できる。もともとの仕様では画像がアップロードされた場合のみcanvasの更新を行っていたため、対象外のファイルがアップロードされた場合は、過去にアップロードされたデータや子要素が画面に残り続ける。それを解消するため、PNG以外がアップロードされた場合は一度canvasを削除する仕様とした。また、同様にダウンロード用URLが存在する場合は子要素から削除する。

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

## 2つの配列から1つのオブジェクトを作成する

### detail

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

### reference

1. [Object.fromEntries()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries)

## １０進数と16進数を相互変換する

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

### reference

1. [parseInt](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/parseInt)
2. [toString](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)
3. [英語で16進数はHexadecimalでいいのでしょうか？他のn進数はどう表すのでしょうか？](https://q.hatena.ne.jp/1086764371)

## Symbol型とはプリミティブな唯一無二のIDを生成する

### detail

symbol型はプリミティブなデータ型で、`Symbol()`関数はsymbol型の値を返す。このsymbol型のデータは必ずユニークなIDであり、文字列や数値のようなデータとは扱いが異なる。また、コンストラクターとしての機能を持たないので、`new Symbol()`と書くとエラーになる。

```typescript
const hoge = Symbol();

console.log(hoge === Symbol()); // false
console.log(Symbol() === Symbol()); // false
console.log(hoge === hoge); // true

const fuga = Symbol('fuga');

console.log(fuga === Symbol('fuga')); // false
console.log(Symbol('fuga') === Symbol('fuga')); // false
console.log(hoge === fuga); // false
console.log(fuga === fuga); // true
```

`Symbol() === Symbol()`が**false**になるのが特徴的で、これがsymbol型たる所以。つまり1番目の`Symbol()`と2番目の`Symbol()`は、コード上の見た目こそ同じであるものの、返す値が異なっていることがわかる。`===`で比較してtrueになるようなものを、後で`Symbol()`を使って生成できないのがsymbol型。じゃあ実際に返ってきている値を見られるかというと、これは**無理**。`console.log(Symbol('piyo'));`と実行しても`Symbol(piyo)`と返ってくるだけで、実際の値そのものが見られるわけではない。これに関しては、こういう仕様だとあきらめるしかない。

```typescript
const hoge = Symbol();
const obj = {
    [hoge] : 'foo',
};

const objAlt = {
    hoge: 'bar',
};

console.log(obj[hoge]); // 'foo'
console.log(obj.hoge); // undefined
console.log(obj['hoge']); // undefined

console.log(objAlt[hoge]); // undefined
console.log(objAlt.hoge); // 'bar'
console.log(objAlt['hoge']); // 'bar'

const fuga = hoge;

console.log(hoge === fuga); true
console.log(obj[fuga]); // 'foo'
console.log(obj[hoge] === obj[fuga]); // true
```

`Symbol()`で生成したIDは、オブジェクトのキーに設定できる。このとき、プラケット（`[]`）を記述するのとしないのとで、生成したオブジェクトの挙動が変わるので注意が必要。プラケットを付与しなかった場合、それは`hoge`という文字列でキーが作成されるため、`objAlt.hoge`や`objAlt['hoge']`という記法でアクセスが可能。逆にこれ以外の記法だと結果が`undefined`になる。

対して、symbol型によるキー設定はプラケットを付与した場合のみ有効となるため、プラケットを付与したオブジェクトのキーにアクセスする場合は`obj[hoge]`と記述する必要がある。これ以外の記法、たとえばピリオド使いでプロパティにアクセスしようとすると結果は`undefined`になる。

このsymbol型を有効活用しようとするなら、「ビルトインクラスに対し安全に拡張を行える」などのメリットはあるもののそこまで有用かと言われると正直微妙。どちらかというとJSの互換性保持のために実装されたという側面があり、既存のライブラリについて動作を（ある程度）保証しつつも言語拡張を行う策と考えられる。つまり、JavaScript（というかECMAScript）の仕様策定側には必須だが、一般的な開発者にとってはそこまで有用というほどでもない。

ただ、こういう唯一無二の仕様を持つため、今後実装される別機能と組み合わせると途端に有用にならないとも言えず、一応でもその機能と挙動を理解しておくに越したことはない。

### reference

1. [Symbol](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
2. [JavaScript互換性の要！Symbol（シンボル）について](https://analogic.jp/symbol/)
3. [ECMAScript6にシンボルができた理由](https://qiita.com/naruto/items/312adeb6145eb6221be7)
4. [Symbolについて](http://js-next.hatenablog.com/entry/2014/03/01/001650)
