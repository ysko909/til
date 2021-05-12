# TIL - TypeScript

TSに関することはここに書く。

## 型宣言いろいろ

### detail

TypeScriptは変数や関数の引数、戻り値など様々な場面でその値の型を表現する。プリミティブ型については、たとえば`let hoge: string;`のように記述したりする。プリミティブ型については比較的見た目がわかりやすいのだが、オブジェクト型リテラルになると記述方法が多彩で複雑なので、ここで整理しておく。もちろん、これ以外の記述方法もあると思うが、普段利用頻度の高い主だったところはこんなところではないだろうか。

```typescript
type TypeSample = {
    // なんでもあり
    anything: any;
    
    // プリミティブ型
    str: string;
    num: number;
    bool: boolean;
    
    // ユニオン型
    strOrNum: string | number;

    // 以下は全てオブジェクト型リテラル
    arrayNum : number[]; // 数値の配列
    arrayStr : string[]; // 文字列の配列
    arrayBool : boolean[]; // 真偽値の配列
    arraAny : any[]; // なんでもありの配列
    arrayObj : {[key:string]: (number | string)}[]; // キー名が文字列で数値あるいは文字列の値を持つオブジェクトの配列
    multiArray : any[][]; // なんでもありの2次元配列

    obj: {key: any}; // キー名が`key`で値がなんでもありであるオブジェクト
    arrayInObj: {key: any[]}; // キー名が`key`で値がなんでもありな配列であるオブジェクト
    multiArrayInObj: {key: any[][]}; // キー名が`key`で値がなんでもありな2次元配列であるオブジェクト
    stringKey: {[key: string]: any}; // キー名が文字列で値がなんでもありであるオブジェクト
    numberKey: {[key: number]: any}; // キー名が数値で値がなんでもありであるオブジェクト
    stringKeyAndArrayInObj: {[key: string]: (number | string)[]}; // キー名が文字列で値が数値か文字列の配列であるオブジェクト
    stringKeyAndArrayInObjPart2: {[key: string]: number[] | string}; // キー名が文字列で値が「数値のみの配列」か文字列であるオブジェクト

}

let sample : TypeSample = {
    anything: [1, 2, 'a'],

    str: 'hoge',
    num: 999,
    bool: false,
    strOrNum: 'foo',

    arrayNum : [1, 2, 3, 4, 5],
    arrayStr : ['foo', 'bar', 'baz'],
    arrayBool : [true, false, false],
    arraAny : [{'hoge': 1}, true, 'foo', 123],
    arrayObj : [{'ham': 1}, {'eggs': 'foo', 'spam': 321}, {'foo':1, 'bar': 2, 'baz': 'bazbazbaz'}],

    multiArray : [[1, 2, 'hoge'], ['fuga', 3, 'piyo'], ['ham', 'eggs', 'spam', `python`]],

    obj: {key: 'hoge'},
    arrayInObj: {key: [true, [0, 1, 2, 3], {'hoge': 1, 2: 'fuga'}, 999, 'piyo']},
    multiArrayInObj: {key: [['ham', 1, {'foo': [9, 8, 7]}, 2], ['eggs', true], [false, 10, 'spam', [3, 2, 1, 0]]]},
    stringKey: {'hoge': [1, 2, 3], 'fuga': true, 'piyo': {1: 'foo'}},
    numberKey: {1: false, 2: [3, 2, 1], 999: {'hoge': 'fuga'}},
    stringKeyAndArrayInObj: {'ham': [1, 2, 3], 'eggs': ['hoge', 1, 'fuga', 2, 'piyo'], 'spam': ['foo', 'bar', 'baz']}
    stringKeyAndArrayInObjPart2: {'ham': [1, 2, 3], 'eggs': 'hoge', 'spam': 'foo'}
}
```

ユニオン型で定義する際、`(number | string)[]`と`number[] | string[]`は意味合いが異なることに注意。前者は「数値あるいは文字列を要素に含んだ配列」だが、後者は「数値のみを含んだ配列、または文字列のみを含んだ配列」を指す。

### reference

1. [Everyday types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
1. [TypeScript のオブジェクト型リテラルいろいろ](https://qiita.com/hida/items/fb0379353bbb71f8191b)

## TypeScriptでオブジェクトの型を定義するときは`interface`か`type`を使う

### detail

TypeScriptで、オブジェクトのプロパティについて型定義する方法は以下の通り。

```typescript
interface Person {
    firstName: string;
    lastName: string;
}
```

`interface`を使ってオブジェクトの名前を宣言し、プロパティの型をそれぞれ定義する。これと似たような機能を持つ`type`というものが存在する。

```typescript
type Person = {
    firstName: string;
    lastName: string;
}
```

`interface`がクラスやオブジェクトの**規格を定義する**のに対し、`type`は**型や型の組み合わせに名前をつけている**。見た目だけだとイコールが付いている以外はどっちも一緒じゃねーか、と思うが実際の挙動は多少異なる。

1. `interface`は宣言マージが可能。宣言マージとは、同じファイルやモジュール内に複数の同じ名前を持つ`interface`が存在した場合、自動的にまとめられて（マージされて）1つの`interface`として扱う機能。
2. `interface`は`extends`、つまり継承が可能。
3. `type`はあくまで型に名前をつける、という機能を提供しているに過ぎないので、比較的記述方法がバリエーションに富んでいる。Union型が宣言できるのも`type`の特徴。まあ、バリエーションに富んでいるというのは、裏を返すと選択肢が多いのでどれを選ぶか取捨選択が必要になる、という意味でもあるが。

他にもあるが大きな違いがこのあたり。じゃあ、どっちを使えばいいのだろうか。考え方はいくつかある。ただ、基本的には`type`を使ったほうがいいんじゃないだろうか。

1. 大前提として、チームでの開発ではチーム標準に準拠する。
2. 宣言マージが必要なら`interface`を選ぶ。
3. `type`は様々な型の定義ができるため、`interface`では記述できなくても`type`で統一が可能。

上記の例ではオブジェクトの型の組み合わせに名前をつけているだけなので、`interface`も`type`も対して違いはない。が、Union型などの宣言は`interface`で実装できない。よって、`type`を基本的に使って、どうしてもというシチュエーションでのみ`interface`を使う運用がいいと思われる。

### reference

1. [基本的な型付け](https://future-architect.github.io/typescript-guide/typing.html)
2. [【翻訳】TypeScriptにおけるInterfaceとType aliasの違い](https://freelance-jak.com/technology/typescript/1988/)
3. [【TypeScript】type の使いどころが知りたい話](https://mslgt.hatenablog.com/entry/2019/12/06/063526)

## TypeScriptには「function型」という型がある

### detail

```typescript
interface Props{
  width?: number;
  height?: number;
  onClick?: Function;
}
```

とあるところで、上記のようなコードを見かけた。上の2つはわかるとして、`function`って型を指定できるの？って話だが、結論から言えばTypeScriptは**できる**。

```typescript
// a, bは任意の値
// 2つの引数を受け取ってnumber型を返すことを示しており、このような構造の
// functionしか代入できない
  onClick?: (a: number, b: number) => number;
```

代入される関数の引数と戻り値の型が決まっている場合は、上記のように記述した方がよい。functionの内容を詳細に記述することで、余計な代入時にチェックが働くようになる。

### reference

1. [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
1. [TypeScript ~基礎編~ 型](https://selfnote.work/20200726/programming/typescript-basic-type/)

## TypeScriptでのクラス宣言と継承について

### detail

TypeScriptでのクラス宣言と継承について、挙動をメモする。

```typescript
class Hoge {
  x: number;
  y: number;
  
  constructor(x: number, y: number){
    this.x = x;
    this.y = y;
  }
  
  add(z: number){
    return new Hoge(this.x + z, this.y + z);
  }
}

let hoge: Hoge = new Hoge(1, 2);
console.log(hoge);
console.log(hoge.add(3));
console.log(hoge.constructor.name);

class Fuga extends Hoge {
  xx: string;
  yy: string;
  constructor(x: number, y:number, xx: string, yy: string){
    super(x, y);
    this.xx = xx;
    this.yy = yy;
  }
  add_fuga(z: number, zz: string){
    return new Fuga(this.x + z, this.y + z, this.xx + zz, this.yy + zz);
  }
  add_fuga_super(z: number){
    return super.add(z);
  }
  
}

let fuga: Fuga = new Fuga(10, 11, 'foo', 'bar');
let fuga2: Fuga = fuga.add(9)
let fuga3: Fuga = fuga.add_fuga(99, 'baz');
let fuga4: Fuga = fuga.add_fuga_super(123);
console.log(fuga);
console.log(fuga2);
console.log(fuga3);
console.log(fuga4);
console.log(fuga.constructor.name);
console.log(fuga2.constructor.name);
console.log(fuga3.constructor.name);
console.log(fuga4.constructor.name);

class Piyo extends Hoge {
  x : string;
  constructor(y: number){
    super('string x', y);
  }
  add(y: number){
    return new Piyo(y);
  }
  
}

let piyo:Piyo = new Piyo(2222);
let piyo2:Piyo = piyo.add(9999);
console.log(piyo);
console.log(piyo2);
console.log(piyo.constructor.name);
console.log(piyo2.constructor.name);

```

- TypeScriptでのクラス宣言は、`class`で宣言しプロパティを型情報とともに記述する。
- 継承する場合`extends`で継承元のクラスを指定する。このとき、コンストラクタが存在する場合は`super()`で親コンストラクタを呼び出さないといけない。`super()`を読みだした後で、必要な処理を追加できる。
- ちなみに、`.constructor.name`でクラス名を返してくれる。

```console
Object {
  x: 1,
  y: 2
}
Object {
  x: 4,
  y: 5
}
"Hoge"
Object {
  x: 10,
  xx: "foo",
  y: 11,
  yy: "bar"
}
Object {
  x: 19,
  y: 20
}
Object {
  x: 109,
  xx: "foobaz",
  y: 110,
  yy: "barbaz"
}
Object {
  x: 133,
  y: 134
}
"Fuga"
"Hoge"
"Fuga"
"Hoge"
Object {
  x: "string x",
  y: 2222
}
Object {
  x: "string x",
  y: 9999
}
"Piyo"
"Piyo"
```

- 親クラスのメンバ関数を呼び出す場合、`super.`構文で呼び出せる。なお、上記のコードでは`Fuga`クラスにおいて親クラス`Hoge`のメンバ関数`add()`を呼び出す場合、**処理内容や戻り値は親クラスに記述されている内容に準拠する**。よって、`supeer.add()`を呼び出す`add_fuga_super()`の戻り値は、`Fuga`クラスではなく`Hoge`クラスであることに注意。
- ちなみに上記のコードはCodepenのTypeScript環境で実行している。`add_fuga_super()`の戻り値が`Hoge`クラスであるにもかかわらず、変数宣言時に`Fuga`クラスであると宣言しているが、これはエラーにならない。そういうものなのか・・・（`Fuga`を`Hoge`や`Piyo`に書き換えてもエラーとかにはならなかったので、少なくともCodepenのTS環境はこういうものらしい）。

```typescript
class GameScene extends Phaser.Scene {
    constructor(sceneName) {
        super(sceneName);
    }

    preload() {

    }

    create() {
      this.add.text(100, 100, 'hoge');

    }
}

class MainScene extends GameScene {
    constructor() {
        super('mainScene');
    }

    preload() {

    }

    create() {
      super.create(); // `super.method()`とすると基底クラスのメソッドを実行する
      this.add.text(200, 200, 'fuga');
    }
}

let mainScene = new MainScene();

let config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    backgroundColor: '#e0e0e0'
};

let game = new Phaser.Game(config);

game.scene.add('mainScene', mainScene);

game.scene.start('mainScene');
```

- `extends`した派生クラス内で、基底クラスのpublicメソッドを利用する場合は`super.method()`と記述すると実行できる。
- 上記の例では、基底クラス側で「hoge」という文字列を、派生クラス側で「fuga」という文字列を表示している。`super.create()`を実行しない場合、「fuga」のみが画面に表示される。これは、基底クラスにあったpublicメソッドである`create()`を、派生クラス側でオーバーライドしているため。
- [オーバーライド](https://en.wikipedia.org/wiki/Method_overriding)は乱暴に要約すると、メソッド名や引数はそのままに、派生クラス側で処理内容を上書きしてしまうこと。ここでは、基底クラスの`create()`メソッドが「hoge」を表示するという処理であるのに対し、派生クラスの`create()`は同じメソッド名でありながら「fuga」を表示する処理に置き換わっている。そのままだと基底クラス側の`create()`は実行されない。オーバーライドされる前のメソッドを実行するためには、上記の通り`super.create()`と記述して、**基底クラス側の**`create()`を実行するよと明示する必要がある。

### reference

1. [クラス](https://typescript-jp.gitbook.io/deep-dive/future-javascript/classes)
1. [クラス](https://docs.solab.jp/typescript/class/inheritance/)
1. [JSのクラス名をゲットする](https://qiita.com/makerbox/items/3f6ed31abbb128f5edb1)
1. [JavaScriptの「型」の判定について](https://qiita.com/south37/items/c8d20a069fcbfe4fce85)

## 型定義ファイルがないよと言われたらインストールが必要

### detail

TypeScriptの環境でコードを書き始めると（場合によってだと思うが）エラーを吐くことがある。

```console
Could not find a declaration file for module ‘react’.
```

エラーメッセージはこれ。今回は`create-react-app`を実行した環境において、コードを書き始めた際に上記のエラーが表示された。

このメッセージで調べてみるとtsconfig.jsonに、`“noImplicitAny”: false,`と書けばいいと言っているブログの記事があったりする。このオプションがTrueだと、型定義のない変数を暗黙のany扱いすることを禁止する。つまりanyを使いたいなら明示的にanyを書かなければいけない、という制約を課すオプション。逆に言えばFalseではこの制約が働かない。ということは素のJavaScriptを書いているのと大差ないわけで、TypeScriptを使っている意味がなくなってしまう。

じゃあどうするかというと、型情報を定義した**型定義ファイルをインストールする**必要がある。

「型情報のインストールってなんのこっちゃ」という感じだが、TypeScriptは型を識別する都合上「型定義ファイル」というものを必要とする。JSはもともと動的型付けだから型を明記することはないし、ReactはもともとTypeScriptで記述されておらず、create-react-appしただけでは型情報が提供されない。よって、別途型情報を用意してやる必要がある。

型情報は、メジャーなJSライブラリだとすでに用意されていることが多い。既存の型情報を利用するには@typesを使う。

```console
yarn add -D @types/react
```

上記のように`@types/ライブラリ名`で実行すれば、既存の型定義ファイルがインストールされる。

```console
$ yarn add @types/phaser
error An unexpected error occurred: "https://registry.yarnpkg.com/@types%2fphaser: Not found".
```

型情報が存在しない場合は、上記のように「そんな型定義ファイルはないよ」とエラーになる。既存の型定義ファイルがないような場合は、[オリジナルで作成することも可能](http://typescript.ninja/typescript-in-definitelyland/definition-file.html)。

なお、型情報が必要なのはあくまで開発中だけ。型情報があろうとなかろうと、リリースするパッケージに含める必要はないので-Dオプションをつけて開発用の依存関係としてインストールする。

### reference

1. [Adding TypeScript](https://create-react-app.dev/docs/adding-typescript/)
2. [【TypeScript】【React】Could not find a declaration file for module ‘react’.の対処について](https://qiita.com/waniwaninowani/items/7ea8b4eacab296758c19)
3. [TypeScript で型定義ファイル( d.ts )がないときの対処法](https://qiita.com/Nossa/items/726cc3e67527e896ed1e)
4. [JavaScriptの資産と@types](http://typescript.ninja/typescript-in-definitelyland/at-types.html)

## インデックスシグネチャで連想配列を使う

### detail

TSにて連想配列を取り扱う際に出てくるのがインデックスシグネチャ。インデックスシグネチャを用いて、連想配列に含まれるキーや値の型を指定できる。

```typescript
interface Hoge{
    [key:string]:number;
}

const hoge: Hoge ={
    foo:1,
    bar:2,
    baz:3
};

console.log(hoge['foo']);
console.log(hoge.foo); // ピリオドでもOK
hoge.foo = 100;
console.log(hoge['foo']);
console.log(hoge.foo);

// hoge.foo = 'hoge'; これは「値はnumberだからstringは代入できないぜ」エラー

hoge.foa = 9999; // 「foo」を入力しようとしてタイポ
console.log(hoge.foo);
console.log(hoge.foa);

// hoge.foo.foobarbaz = 9999; これは「プロパティなし」エラー
```

`interface`を使って、連想配列のキーと値の型をそれぞれ指定する。ここではキーがstring、値がnumberとした。この設定に一致する場合は、値の設定が可能。

```console
1 
1 
100 
100 
100 
9999 
```

ちなみに、連想配列は**もともとなかったキーと値を新たに設定することができてしまう**。「もともと存在するキー`foo`の値を書き換えようとして、タイポしたキー`foa`に値を設定してしまう」という可能性はまあまあ考えられるので注意が必要。なお、この場合は新しい要素として追加される。そのため、`foo`が新しい値に置き換わっていると思い込んで参照してみると、古い値が返ってくる（新しい値は`foa`側に設定されてしまっている）のでバグの原因にもなりかねない。

### reference

1. [第9回　連想配列の取り扱い方](https://www.atmarkit.co.jp/ait/articles/1501/29/news117_4.html)
2. [Index signature（インデックス型）](https://typescript-jp.gitbook.io/deep-dive/type-system/index-signatures)
3. [TypeScript：インデックスシグネチャが型安全を破壊する例](https://qiita.com/aakasaka/items/0b081c90b1b99b82143c)

## TypeScriptのチュートリアルをやってみる

### detail

TypeScriptの基本である型の指定方法について記述する。なお、ここでのソースは[TypeScript Tooling in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-tooling-in-5-minutes.html)のソースコードを少し改変している。環境は`npx create-react-app my-app --template typescript`で構築しているが、CodepenでのTypeScript環境でも、`import`文などを除けば挙動は同じだと思う。

```tsx
import React from 'react';
import ReactDOM from 'react-dom';

function greeter(person) {
    return 'Hello, ' + person;
}

function greetWithName(user = 'John Doe') {

    return greeter(user);
}

function App() {
    return (
        <>
            <h2>{greetWithName()}</h2>
        </>
    );
}

ReactDOM.render(
    <App />,
    document.getElementById('root'),
);

```

チュートリアルにあるコードをReact用に拡張し、TSX形式で書いた。ただし、上記のコードは実行しようと`yarn start`すると、もとのチュートリアル通りエラーを吐く。

```console
TypeScript error in /workspace/my-app/src/index.tsx(4,18):
Parameter 'person' implicitly has an 'any' type.  TS7006

    2 | import ReactDOM from 'react-dom';
    3 | 
  > 4 | function greeter(person) {
```

エラー内容は上記の通り。エラー内容は「暗黙的に`any`が使われてるぞ」という意味で、TypeScriptでは基本的に**型を明示しなければならない**設定になっている。つまりこの場合では、`person`の型を明示的に指定する必要がある。

```tsx
function greeter(person: string) {
    return 'Hello, ' + person;
}
```

型の指定方法は`変数: 型`で指定できる。上記では`person`は文字列だよ、と指定していることになる。上記の内容で変更したら`yarn start`すると、今度は正常に動作するようになる。このように、TypeScriptでは変数やメンバに型を指定することが求められる。

```tsx
function App() {
    return (
        <>
            <h2>{greetWithName([0, 1, 2])}</h2>
        </>
    );
}
```

逆に文字列ですよと指定されているにもかかわらず配列などを指定した場合は、想定している型と実際に入力された型が異なるためエラーになる。

```console
TypeScript error in /workspace/my-app/src/index.tsx(16,33):
Type 'number' is not assignable to type 'string'.  TS2322

    14 |     return (
    15 |         <>
  > 16 |             <h2>{greetWithName([0, 1, 2])}</h2>
```

文字列を想定しているのに数値（の配列）なんてアサインできねーよ、とエラーになった。

```tsx
import React from 'react';
import ReactDOM from 'react-dom';

interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person: Person) {
    return 'Hello, ' + person.firstName + ' ' + person.lastName;
}

function greetWithName(user = { firstName: 'Jane', lastName: 'Doe' }) {

    return greeter(user);
}

function App() {
    return (
        <>
            <h2>{greetWithName()}</h2>
        </>
    );
}

ReactDOM.render(
    <App />,
    document.getElementById('root'),
);

```

`interface`を利用すると、オブジェクトのプロパティに対しそれぞれの型が指定できる。`type`でももちろんOK。

### reference

1. [TypeScript Tooling in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-tooling-in-5-minutes.html)
1. [コンストラクタを定義する](https://typescript-jp.gitbook.io/deep-dive/future-javascript/classes#konsutorakutawosuru)
1. [コンストラクタの引数を使ってプロパティを宣言](https://future-architect.github.io/typescript-guide/class.html#id4)
1. [クラス宣言](https://docs.solab.jp/typescript/class/declaration/)
2. [Typescript: TS7006: Parameter 'xxx' implicitly has an 'any' type](https://stackoverflow.com/questions/43064221/typescript-ts7006-parameter-xxx-implicitly-has-an-any-type)

## コンストラクタ引数にアクセス修飾子を付与してコードを簡略化する

### detail

クラス宣言時のコンストラクタ引数にアクセス修飾子を付与することで、プロパティの宣言から初期化までのコードを簡略化できる。

```tsx
import React from 'react';
import ReactDOM from 'react-dom';

class Student {
    fullName: string;
    constructor(
        public firstName: string,
        public middleInitial: string,
        public lastName: string
    ) {
        this.fullName = firstName + ' ' + middleInitial + ' ' + lastName;
    }
}

interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person: Person) {
    return 'Hello, ' + person.firstName + ' ' + person.lastName;
}

function greetWithName(user = { firstName: 'Jane', lastName: 'Doe' }) {

    let student = new Student('John', 'M.', 'Doe');

    return greeter(student);
}

function App() {
    return (
        <>
            <h2>{greetWithName()}</h2>
        </>
    );
}

ReactDOM.render(
    <App />,
    document.getElementById('root'),
);

```

クラスも基本的には`interface`と同様で、プロパティに対し型を設定する。コンストラクタの引数に`public`が付与されているが、これが**アクセス修飾子**。クラスのプロパティはコンストラクタの前に記述することで宣言するが、コンストラクタの引数にアクセス修飾子を付与することで、**プロパティを自動的にクラス内に定義し、コンストラクタの引数によって初期化されるまでを省略することが可能**になっている。つまり、事前に宣言しコンストラクタ内で初期化するコードを書かなくてもよい、ということ。

```tsx
class Foo {
    constructor(public x:number) {
    }
}
```

たとえば、上記のようにコンストラクタの引数にアクセス修飾子`public`を付与した場合を考える。この場合、プロパティの宣言や初期化は記述しなくても動作する。

```tsx
class Foo {
    x: number;
    constructor(x:number) {
        this.x = x;
    }
}
```

アクセス修飾子を付与しない場合、メンバの宣言とコンストラクタの引数による初期化を記述する必要がある。その場合は上記のように記述するが、これらのコードはコンストラクタの宣言時に引数にアクセス修飾子を付与することで省略が可能。なお、メンバに対するアクセス修飾子を省略した場合、そのメンバは`public`扱いとなる。なので、上記は`public x: number;`と宣言しているのと同じ。

このように、コンストラクタの引数にアクセス修飾子を付与するだけで、プロパティの宣言から初期化までのコードを簡素化できるので、コンストラクタの引数にアクセス修飾子を付与することをクセにした方がいいと思う。

### reference

1. [TypeScript Tooling in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-tooling-in-5-minutes.html)
1. [コンストラクタを定義する](https://typescript-jp.gitbook.io/deep-dive/future-javascript/classes#konsutorakutawosuru)
1. [コンストラクタの引数を使ってプロパティを宣言](https://future-architect.github.io/typescript-guide/class.html#id4)
1. [クラス宣言](https://docs.solab.jp/typescript/class/declaration/)

## アクセサを記述する

### detail

TypeScriptは、オブジェクトのメンバに対するgetter/setterを提供している。

```tsx
import React from 'react';
import ReactDOM from 'react-dom';

class Student {
    private _fullName: string;
    constructor(
        public firstName: string,
        public middleInitial: string,
        public lastName: string
    ) {
        this._fullName = firstName + ' ' + middleInitial + ' ' + lastName;
    }

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        this._fullName = newName;
    }
}

interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person: Person) {
    return 'Hello, ' + person.firstName + ' ' + person.lastName;
}

function greetWithName(user = { firstName: 'Jane', lastName: 'Doe' }) {

    let student = new Student('John', 'M.', 'Doe');

    console.log(student.fullName);
    student.fullName = 'hoge fuga piyo';
    console.log(student.fullName);
    console.log(student);

    return greeter(student);
}

function App() {
    return (
        <>
            <h2>{greetWithName()}</h2>
        </>
    );
}

ReactDOM.render(
    <App />,
    document.getElementById('root'),
);

```

- `get fullName()`と記述するとgetter。
- `set fullName()`と記述するとsetter。値の設定時に何らかの処理を入れたい（パスワードを要求するとか）場合、setterに記述する。

なお、あんまりそんな書き方をすることはないだろうが、getterとsetterで異なる型をのアクセサを記述することはできず、必ず同一の型で揃える必要がある。

ちなみに、`private`なプロパティの名前にはアンダーバー`_`を1つ付与するのが慣例っぽいが、TypeScriptのドキュメントでは`private`の説明部分でも[アンダーバーを記述していない](https://www.typescriptlang.org/docs/handbook/2/classes.html#private)ので、不特定多数に配布する予定のあるライブラリを作成するケース以外はあまり気にしなくていいのかもしれない。ただし、どちらの場合もアクセサの名前自体にはアンダーバーは付与しなくてよい。

```console
John M. Doe index.tsx:36
hoge fuga piyo index.tsx:38
{...}
_fullName: "hoge fuga piyo"
firstName: "John"
lastName: "Doe"
middleInitial: "M."
<prototype>: Object { … }
```

実行結果から、setterによってメンバ`_fullName`は書き換わっているが、その他のメンバは元のまま。ちなみに他のメンバは個別に宣言していないが、コンストラクタの引数にアクセス修飾子を付与したため、メンバの宣言や初期値設定を省略している（前回の内容を参照）。

### reference

1. [Classes](https://www.typescriptlang.org/docs/handbook/2/classes.html#getters--setters)
1. [クラス](http://js.studio-kingdom.com/typescript/handbook/classes#accessors)

## `Property xxx has no initializer and is not definitely assigned in the constructor`エラーは初期値を代入する

### detail

自分オリジナルのクラスを用いたコードを実行する際、`Property xxx has no initializer and is not definitely assigned in the constructor`というエラーが表示されることがある。`xxx`の部分はプロパティ名が入っており、「このプロパティ、初期化されてないしコンストラクタの中でも代入されてないぞ」と言っている。

```typescript
class C {
  foo: number;
  bar = "hello";
  baz: boolean;
  //  ~~~
  //  Error! Property 'baz' has no initializer and is not definitely assigned in the
  //         constructor.
  constructor() {
    this.foo = 42;
  }
}
```

上記だと`baz`というプロパティについてエラーが発生しており、「`baz`ってプロパティが初期化されてないぞ」と言っている。実際、エラーの表示されていないプロパティの`foo`はコンストラクタ内で値が設定されているし、`bar`は宣言時に初期値が代入されている。`baz`もどちらかの方法で初期値を設定すれば、このエラーを解消できる。

エラーの原因はまぁエラー文のとおりなんだが、TypeScriptはプロパティについて宣言時またはコンストラクタ内において、プロパティに初期値を設定することを強制するフラグが存在する。`strictPropertyInitialization`のことだが、これが有効である場合は初期値の設定を強制する。値の設定忘れやタイプミスなどを防止するためにの、このフラグは有効にしておくべき。

### reference

1. [TypeScript complain “has no initializer and is not definitely assigned in the constructor” about constructors by returning constructed object](https://stackoverflow.com/questions/54104187/typescript-complain-has-no-initializer-and-is-not-definitely-assigned-in-the-co)
2. [Strict Class Initialization](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-7.html#strict-class-initialization)

## ジェネリクスを使う

### detail

ジェネリクスはJavaなどにも搭載されている機能で、抽象的な型引数を利用して再利用が可能なクラスや関数などのコンポーネントを作成する機能。ジェネリクス自体は奥の深いもので、クラスの宣言にも利用できたり型の制限を設けたりと機能は多彩。ここでは、基本的な利用方法について記述する。

たとえば入力された値をそのまま返す、`echo`のような関数を考える。この関数からすると、引数がnumberなのかstringなのかは実際に呼び出されるまでわからないため、戻り値の型も呼び出されるまではわからない。このとき、単純に実装するなら`any`で型を指定する方法がある。

```typescript
function func(arg: any):any{
    return arg;
};

console.log(func('hoge'));
console.log(func(123));
```

これで、文字列でも数値でも受け取れる関数になる。ただし、戻り値は`any`となってしまい、関数の戻り値としての情報はないに等しい。本来は引数がstringなら戻り値もstring、引数がnumberなら戻り値もnumberとしてほしいのだが、`any`で指定しまうとこれらの欲しい情報が揮発してしまう。様々なデータ型を扱いつつ引数に対応した戻り値の型になるなど、再利用が可能な関数として宣言したい。こういう場合にジェネリクスが使える。

```typescript

function funcGen<T>(arg: T):T {
    return arg;
};

console.log(funcGen<string>('hoge'));
console.log(funcGen<number>(123));
```

ジェネリクスを使った関数宣言で記述した。ジェネリクスとして型を動的に指定するためには、関数名の直後に`<>`で**型引数**を指定することで実現する。このように型引数で型を指定すれば、引数だけでなく戻り値の型も指定できる。これで`any`を使って型を指定した時と異なり、戻り値の型情報が揮発することはない。

```typescript
console.log(funcGen('fuga'));
console.log(funcGen(987));
```

また、上記のように型を指定せずに関数を実行することも可能。この場合、コンパイラが引数の型を参照して自動的に型を判別し、戻り値の型にも反映してくれる。型の推論はコードを多少読みやすくしてくれるが、複雑なコードではコンパイラが型の推論に失敗しないとも限らず、そのような場合では明示的に型引数で指定する方が安全。

なお、アロー関数はfunctionで関数を宣言するのと記述形式が若干異なり、型引数に**カンマ**を記述する必要がある。

```typescript
function foo<T>(x: T): T { return x; } // 基本

const foo = <T>(x: T): T => x; // これはダメ。単純にアロー関数の形式で記述するとエラーになる

const foo = <T,>(x: T): T => x; // 型引数にカンマが必須
const foo = <T extends unknown>(x: T) => x; // 古いTypeScriptではこのようにunknownをextendsする必要がある
```

アロー関数でのジェネリクス記述時の特徴として、型引数にカンマを必ず記述する。これにより、アロー関数でもジェネリクスが書けるようになる。

### reference

1. [【TypeScript】Generics(ジェネリックス)を理解する](https://qiita.com/k-penguin-sato/items/9baa959e8919157afcd4)
2. [ジェネリクス](http://js.studio-kingdom.com/typescript/handbook/generics)
3. [What is the syntax for Typescript arrow functions with generics?](https://stackoverflow.com/questions/32308370/what-is-the-syntax-for-typescript-arrow-functions-with-generics)

## 関数の引数にオブジェクトを指定する方法

### detail

関数の引数にオブジェクトを指定する場合の方法について記述する。

```typescript
const func = (arg:{x: string, y: string, z: string}): string => arg.x + arg.y + arg.z;

const func2 = (arg:{x: number, y: number, z: string}, arg2: string) => {
    return arg.x.toString() + arg.y.toString() + arg.z + arg2;
}

console.log(func({x:'foo', y: 'bar', z: 'baz'}));

console.log(func2({x: 1, y: 2, z: 'funcfunc'}, 'fugafuga'));
```

引数のオブジェクト自体に名前をつけ、各プロパティにそれぞれ型情報を付与する。引数を参照する場合は、`オブジェクトの名前.プロパティ名`で参照できる。このように、基本的には普段のTypeScriptの書き方で問題ない。

```typescript
type Hoge = {
    foo: number[];
    bar: string;
    baz: Function;
};

const sum = (numbers: number[]) => numbers.reduce((total, value) => total + value);

const func = (hoge: Hoge) => {
    console.log(sum(hoge.foo));
    console.log(hoge.bar.substr(3, 5));
    console.log(hoge.baz('hogehoge'));
}

let hoge:Hoge = {
    foo: [1, 2, 3, 4, 5],
    bar: 'hogefugapiyo',
    baz: (arg: string) => arg + ' in func'
}

func(hoge);

```

上記のように、`type`で定義したオブジェクトを引数に指定することも可能。

ちなみに`sum()`関数は、数値の配列の要素を合算する。案外、要素を合算するような配列のメソッドとかはないっぽい。

### reference

1. [Object types](https://www.typescriptlang.org/docs/handbook/2/objects.html)
2. [TypeScriptの関数を振り返る](https://qiita.com/pochopocho13/items/c92cba15be3f6a1b29a6)

## ReturnTypeを使って関数の戻り値の型を生成する方法

### detail

ライブラリ内の関数を利用する場合において、その関数の戻り値がオブジェクトであるとき自分で`type`や`interface`を記述しなくても、**関数の戻り値を参照して自動的に型情報を生成してくれる機能がある**。それがUtility Typesの一機能で`ReturnType`というもの。

```typescript
type Hoge ={
    foo: string;
    bar: number;
    baz: Array<string | number>;
}

function hoge(): Hoge{
    return {
        foo: 'eggs',
        bar: 1,
        baz: [2, 'ham', 3, 4, 'spam']
    };
}

type HogeReturnType = ReturnType<typeof hoge>

let resultOfHoge: Hoge = hoge();

let resultOfHoge2: HogeReturnType = hoge();

```

型情報の`Hoge`は自分で記述したもの。対して、関数`hoge`の戻り値を`typeof`で調べ、結果を`ReturnType`を用いて格納したのが`HogeReturnType`。こうすることで、自ら記述する手間を省き、誤った内容を記述するリスクを軽減している。

ただし、`ReturnType`で宣言した内容を表示する方法はどうもないっぽい。`console.log(typeof hoge())`を実行すると`Object`としか返ってこないため、そのオブジェクトがどんな型情報を持っているのはわからない。`ReturnType`で得た結果を直接`console.log()`で出力することはどうもできないらしく、TypeScriptのPlaygroundで試してみたらエラーになった。

> 'HogeReturnType' only refers to a type, but is being used as a value here.

「'HogeReturnType'は型情報であって値そのものではない。なのに値として扱われている（表示しようとしている）ぞ」というエラーだ。エラー内容はともかく、型をちゃんと宣言しておけばオブジェクトの構造はIDE上でサジェストされるはずなので、そんなに気にならない・・・はず。

### reference

1. [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
2. [Object Types](https://www.typescriptlang.org/docs/handbook/2/objects.html)
3. [【TypeScript】Utility TypesのReturnTypeを深く理解する](https://qiita.com/NOMURA_keibyou38/items/fc9d21aee217b4213430)
4. [【TypeScript】型定義で関数の戻り値を直接使えるReturnType](https://uiuifree.com/blog/develop/type-script-return-type/)

## 関数の引数をオプションにする方法

### detail

関数宣言時に引数を単純に指定すると、その引数は指定が必須となる。引数の指定を任意にしたい場合は、その引数名に`?`を付与することが必要。

```typescript
const f = (arg: string) => {
    return arg;
}

const f2 = (arg? : string) => {
    return arg;
}

const f3 = (arg1: string, arg2?: string, arg3?: string) => {
    return arg1 + arg2 + arg3;
}

console.log(f('foo'));
console.log(f2('bar'));
console.log(f3('ham', 'eggs', 'spam'));

console.log(f3('ham'));
console.log(f3('ham', 'eggs'));

console.log(f()); // error Expected 1 arguments, but got 0.
console.log(f2());
console.log(f3()); // error "Expected 1-3 arguments, but got 0."

```

関数`f`のように引数名に`?`がない場合、引数を省略し実行しようとしてもエラーになる。しかし、`引数名?`とすることでその引数をオプション扱いにできるので、引数を省略して関数を実行することが可能になる。

オプションの引数と指定必須の引数を、1つの関数に同居させることも可能。この場合、必須の引数が存在するため引数を完全に省略して関数を実行するのは不可能だが、オプションの引数は省略が可能。

### reference

1. [More on functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
2. [TypeScript プログラミング -関数 -引数](https://docs.solab.jp/typescript/function/parameter/)

## 関数の引数にデフォルト値を設定する方法

### detail

関数の引数にはデフォルト値を指定できる。関数実行時に引数が指定されなかった場合は、デフォルト値が採用され関数を実行する。

```typescript
const f = (arg: string = 'hoge') => {
    return arg;
};

console.log(f('foo'));
console.log(f());

console.log(f(''));
```

引数が指定されなかった場合、hogeという文字列が採用される。

```console
"foo" 
"hoge" 
"" 
```

実行結果は上記のとおり。ちなみに、引数にstring形式を指定した場合で空文字を渡すのは、引数に設定なしとは見なされず**空文字が正規の指定になる**ので一応注意が必要。

```typescript
const f2 = (arg1: number = 1, arg2?: number = 2) => { // error "Parameter cannot have question mark and initializer."
    return arg1 + arg2;
}
```

前述の引数をオプションにする`?`とデフォルト値の設定は同居できない。「デフォルト値を設定するくらいなんだから、その引数は必須なんでしょ？オプション扱いしたらデフォルト値はどうするのよ」ということらしい。

### reference

1. [More on functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
2. [TypeScript プログラミング -関数 -引数](https://docs.solab.jp/typescript/function/parameter/)

## Rest引数の使い方

### detail

TypeScriptには**Rest引数**というものがある。これは引数を可変長にできる機能で、ある引数名の**前**に`...`を指定し配列として宣言することで実装できる。

```typescript
const f = (arg1: string, ...arg2: number[]) => {
    let sum: number = arg2.reduce((total, value) => total += value);

    return arg1 + ': ' + sum.toString();
};

console.log(f('hoge', 1));
console.log(f('hoge', 1, 2, 3));
console.log(f('hoge', 1, 2, 3, 4, 5, 6, 7, 8, 9, 10));

const f2 = (...arg: string[]) => {
    arg.forEach(item => {
        console.log(item);
    });
};

f2('ham', 'eggs', 'spam');
```

`...arg2`が可変長の引数で、これをnumber型の**配列**として宣言する。これにより、関数実行時に引数を可変に指定できる。関数内では、可変長の引数は配列として扱うので、`forEach`などで逐次処理ができる。

なお、上記の例ではarg1は必須の引数なので、こっちは指定が必要。

```console
"hoge: 1" 
"hoge: 6" 
"hoge: 55" 
"ham" 
"eggs" 
"spam" 
```

関数実行時、引数を指定したいだけ指定すればよいので、「配列化してから引数として渡す」などの前処理が必要ない。

```typescript
const f = (...arg: Array<number | string | boolean>) => {
    arg.forEach(item => console.log(item));
}

f(1, 'hoge', 2, true, 3, 'fuga', 'piyo', false);

```

可変長の引数に複数のデータ型を指定したい場合、`Array<T>`の形式で指定する。

```typescript
const f = (...arg: Array<number | string>) => {
    arg.forEach(item => console.log(item));
}

let foo = [1, 2, 'ham', 3, 'eggs', 'spam'];

f(...foo);

const hoge = [1];
const fuga = [4, 5];

const f2 = (...arg: Array<number>) => {
    let sum = arg.reduce((total, value) => total += value);
    return sum;
}

console.log(f2(...hoge));
console.log(f2(...fuga));

```

「もともと存在する配列を可変長の引数扱いしたい」という場合は、関数実行時に配列を[スプレッド構文](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Spread_syntax)とともに指定してやることで、配列を展開しつつ引数としての指定が可能。

```typescript
const f2 = (...arg?: string[]) => { // error "A rest parameter cannot be optional."
    return arg;
}
```

ちなみに、Rest引数もオプション扱いできない。

### reference

1. [More on functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
2. [TypeScript プログラミング -関数 -引数](https://docs.solab.jp/typescript/function/parameter/)

## 配列をスプレッド構文で展開し引数に指定すると引数なしエラーになる

### detail

もともとの原因はJSの仕様にあると思うが、メモしておく。

配列をスプレッド構文で展開し、ある関数の引数に指定するときエラーが発生する場合がある。しかも、「この関数はn個の引数が必要なのに、指定された引数は**0個**だぜ」と言われる。実際のエラーメッセージとしては「Expected n arguments, but got 0 or more.」という感じ。

```typescript
const hoge = [1];
const fuga = [4, 5];

console.log(Math.sin(...hoge)); // error "Expected 1 arguments, but got 0 or more."
console.log(Math.atan2(...fuga)); // error "Expected 2 arguments, but got 0 or more."
```

`Math.sin()`は1つの引数を取り、`Math.atan2()`は2つの引数を取る関数。

どちらも、少なくとも1つ以上の要素を含む配列をスプレッド構文で展開して引数として指定しているので、0個の引数指定ということはないはず。なのに、TypeScriptの処理系は引数の指定が0個と見なしている。

```typescript
const fuga = [4, 5];

const f3 = (a:number, b: number) => {
    return a + b;
}

console.log(f3(...fuga)); // error "Expected 2 arguments, but got 0 or more."

```

自作の関数でも、上記のように同様の現象が発生する。これはなぜか。

原因は、TypeScriptは配列の要素が不変であることを**前提にしていない**ため。「今はたまたま要素がいるかもしれんけど、いないこともあるんでない？配列だもの」（誇張表現）と解釈するのか、スプレッド構文で展開したにもかかわらず「引数が0個」として処理する。`const`で変数宣言していたとしても`push`などで要素の操作は可能なため、配列は可変であると扱っているようだ。

```typescript
const hoge = [1] as const;
const fuga = [4, 5] as const;

console.log(Math.sin(...hoge));
console.log(Math.atan2(...fuga));
```

回避策は一応存在していて、`as const`というシグネチャを配列にそれぞれ付与することで、本来不変ではない配列を**不変なものとして扱う**ことができるようになる。もちろん`as const`を付与した配列に対して、`push`など要素を変化させるような処理は実行できない。「変数`hoge`はReadonlyだぜー」とエラーになる。要は、宣言時にハードコーディングされた値でリテラルとして扱われるということ。

不変である配列のスプレッド構文なので、前述のようなエラーが発生せず可変長の引数として関数に渡すことができる。

なお、Rest引数としてスプレッド構文を用いた配列を引数に指定する場合、上記のような問題は発生しない。

ネックなのは、ハードコーディングした値で固定するためコードに依存すること。まぁ、あんまりこんな使い方するシチュエーションはそんな頻繫にはないと思うので、「一応こんなこともできるよ」程度の知識として知っておくくらいでいいかもしれない。あと。なんかのライブラリではこんな書き方をしているらしいので、ライブラリを読むときようの知識としては使えると思う。

### reference

1. [More on functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
2. [【TypeScript】as const ( const assertion )](https://uga-box.hatenablog.com/entry/2020/09/19/000000)



## 'foo' is defined as a property in class 'A', but is overridden here in 'B' as an accessor」と言われたら基底クラスのメソッドをオーバーライドしてるぞ気をつけろ

### detail

長ったらしいエラーメッセージだが、要は「`foo`ってAクラスにプロパティとしてもともと定義されてるけど、Bクラス（派生クラス）で同名のアクセサとしてオーバーライドしてるぞ。いいのか？ダメだろう」っていう意味合いのエラー。

ちなみに自分が遭遇したのは、PhaserJSを使ったコードを書いていたとき。Phaserが持っているクラスを基底クラスとし、派生クラスを定義した。

```typescript
export class Dialog extends Phaser.GameObjects.Container {
    private _x: number;
    private _y: number;
    private _width: number;
    private _height: number;

    private _g: Phaser.GameObjects.Graphics;
    private _r: Phaser.GameObjects.Graphics;

    private _strokePositionMargin: number = 10;

    constructor(scene: Phaser.Scene, position: { x: number; y: number; width: number; height: number; }) {
        super(scene)

        // 中略
    }

    // アクセサ
    get x(): number { // ここでエラー
        return this._x;
    }

    get y(): number { // ここでもエラー
        return this._y;
    }

    get width(): number { // ここでもエラー
        return this._width;
    }

    get height(): number { // ここでもエラー
        return this._height;
    }

}
```

上記のように、4箇所でエラーがそれぞれ発生しているが、どれもエラーメッセージとしては同じ内容。

```console
'x' is defined as a property in class 'Container', but is overridden here in 'Dialog' as an accessor.
'y' is defined as a property in class 'Container', but is overridden here in 'Dialog' as an accessor.
'width' is defined as a property in class 'Container', but is overridden here in 'Dialog' as an accessor.
'height' is defined as a property in class 'Container', but is overridden here in 'Dialog' as an accessor.
```

上記のように、アクセサを定義している部分でエラーが発生している。これらのアクセサは、基底クラスに存在するメンバのアクセサとして機能しているものをオーバーライドしてしまっている。

不味いのは、これらの新規で作ってオーバーライドした側のアクセサが返すプロパティと、オーバーライドされた側のアクセサが返すプロパティが異なる点だ。オーバーライドした側は新規で宣言したプロパティの`_x`などを返すが、オーバーライドされた側が返すのは基底クラスの`x`。見た目が一緒でも返す対象が異なるため、バグの原因になりかねない。

できるなら、新規で宣言するプロパティは基底クラスに存在しないような名前にしておくべき。仮にアンダースコアをつけて宣言したとしても、アクセサはアンダースコアを取って宣言することがあり、プロパティ名そのものでオーバーライドしなくてもアクセサ側でオーバーライドしてしまう可能性がある。

### reference

1. [TS2611: 'foo' is defined as a property in class 'A', but is overridden here in 'B' as an accessor](https://stackoverflow.com/questions/63750710/ts2611-foo-is-defined-as-a-property-in-class-a-but-is-overridden-here-in)
2. [Phaser.GameObjects.Container](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Container.html)

## `Object.fromEntries()`をTypeScriptで使う場合targetがES2019以降じゃないとエラーになる

### detail

```console
let hoge = [10, 11, 12];
let fuga = ['foo', 'bar', 'baz'];

let piyo = [];
hoge.forEach((val, idx)=>{
    piyo.push([val, fuga[idx]]);
});

let foo = Object.fromEntries(piyo);

console.log(foo);
```

上記のようなコードを書いたとき、codepenなどのJS環境では動作するがTS環境だとエラーになる場合がある。

```console
Property 'fromEntries' does not exist on type 'ObjectConstructor'. Do you need to change your target library? Try changing the `lib` compiler option to 'es2019' or later.
```

結論から言うと、tscondig.jsonファイルの`target`という項目が`es2019`以上になっていない場合、エラーが発生する。

> It looks to me that in tsconfig.json, the setting to use is target: "es2019" or greater. This means it won't work in all browsers and typescrcipt does no magic to use this.

これは、`Object.fromEntries()`がes2019において採用された機能であり、それより以前でのTS環境ではコンパイルエラーと扱われるため。JS環境ではそもそも最新版として動作しているが、一度コンパイルが必要なTS環境ではどのバージョンを目的としてコンパイルするかを設定する必要があり、それが`target`で指定されたECMAScriptのバージョンであるわけだ。よって、`target`が`Object.fromEntries()`を実装したES2019以降でなければコンパイルできずにエラーになりまっせ、というメッセージなわけだ。

### reference

1. [Object.fromEntries()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries)
2. [Add ES2019 Object.fromEntries function #30933](https://github.com/microsoft/TypeScript/issues/30933)

## 多次元配列の宣言方法

### detail

```typescript
let hoge:(number | string | symbol)[][];
let fuga:(number | string | symbol)[][][];

hoge = [[1, 2, 'foo'], [3, 'bar', 4, 5], ['baz']];
fuga = [[[1, 2], ['foo'],], [['bar', 3], ['baz', 4, 5, 6], [7]], [[8]], [['ham', 'eggs'], ['spam', 9, 10]]];
```

配列の型宣言は`型[]`だが、複数の型を受け付けてしかも多次元配列の場合は、上記のような指定を行えばいい。2次元配列なら`[][]`でOK。

### reference

1. [「型指定の方法」と「基本型の種類」](https://www.wakuwakubank.com/posts/499-typescript-type/)

## オブジェクトを引数にしたときのオプション指定はプロパティごとに可能

### detail

オブジェクトを関数の引数に指定するとき、プロパティの一部をオプションにしたい場合は、`?`を付与することでプロパティごとにオプション扱いできる。

```typescript
const func=(arg:{hoge: number, fuga: string, piyo: number[]})=>{
    console.log(arg.hoge);
    console.log(arg.fuga);
    console.log(arg.piyo);
};

func({
    hoge: 1,
    fuga: 'foo',
    // Argument of type '{ hoge: number; fuga: string; }' is not assignable to parameter of type '{ hoge: number; fuga: string; piyo: number[]; }'.
    // Property 'piyo' is missing in type '{ hoge: number; fuga: string; }' but required in type '{ hoge: number; fuga: string; piyo: number[]; }'.(2345)
});
```

上記のように、全プロパティが必須であるオブジェクトを引数にとる関数`func`があったとき、あるプロパティを省略すると当たり前だがエラーになる。

```typescript
const func=(arg:{hoge: number, fuga: string, piyo?: number[]})=>{
    console.log(arg.hoge);
    console.log(arg.fuga);
    console.log(arg.piyo);
};

func({
    hoge: 1,
    fuga: 'foo',
});
```

上記のようにオプション扱いしたいプロパティに`?`を付与することで、そのプロパティを省略可能とする。

```console
1 
"foo" 
undefined 
```

なお、オプション扱いしているのでプロパティ`piyo`に値はセットされていない。その状態で上記のコードは実行すると、当たり前だが`undefined`になるので注意が必要。

```typescript

type Hoge = {
    foo: number[];
    bar: string;
    baz: Function;
};

const sum = (numbers: number[]) => numbers.reduce((total, value) => total + value);

const func = (hoge: Hoge) => {
    const {
        foo = [100, 200, 300],
        bar= 'init',
        baz=(arg: string)=>'init: ' + arg
    } = hoge;
    
    console.log(sum(foo));
    console.log(bar.substr(3, 5));
    console.log(baz('hogehoge'));
}

let hoge:Hoge = {
    foo: [1, 2, 3, 4, 5],
    bar: 'hogefugapiyo',
    // baz: (arg: string) => arg + ' in func'
    // Property 'baz' is missing in type '{ foo: number[]; bar: string; }' but required in type 'Hoge'.
}

func(hoge);
```

`type`で引数のオブジェクトの内容を指定する方法でも同じ。全プロパティを必須扱いすると、関数実行時にプロパティの指定がないとエラーになる。

```typescript

type Hoge = {
    foo: number[];
    bar: string;
    baz?: Function; // ?を付与した
};

const sum = (numbers: number[]) => numbers.reduce((total, value) => total + value);

const func = (hoge: Hoge) => {
    const {
        foo = [100, 200, 300],
        bar= 'init',
        baz=(arg: string)=>'init: ' + arg
    } = hoge;
    
    console.log(sum(foo));
    console.log(bar.substr(3, 5));
    console.log(baz('hogehoge'));
}

let hoge:Hoge = {
    foo: [1, 2, 3, 4, 5],
    bar: 'hogefugapiyo',
}

func(hoge);
```

オプション扱いしてやれば、該当するプロパティに関しては省略してもエラーにならない。

### reference

1. [TypeScript の Optional Property](https://ema-hiro.hatenablog.com/entry/2019/01/28/013325)

## `Class 'classB' incorrectly extends base class 'classA'. Property 'hoge' is private in type 'classA' but not in type 'classB'.`と言われたらスーパークラスのプライベートなプロパティを再定義してしまっている

### detail

```typescript
class classA {
    private hoge=1;

    constructor(){
        
    }
}

class classB extends classA {
    public hoge=2; // publicがなくても一緒

    constructor(){
        super();
    }
}
```

上記のようなコードを書くとエラーになる。

`Class 'classB' incorrectly extends base class 'classA'. Property 'hoge' is private in type 'classA' but not in type 'classB'.`

このエラーは「`hoge`っていうプロパティはスーパークラスのプライベートだぞ、サブクラスのじゃないぞ（だから再定義できないぞ）」と言っている。

```typescript
class classA {
    private hoge=1;

    constructor(){
        
    }
}

class classB extends classA {
    private hoge=2; // publicをprivateにした

    constructor(){
        super();
    }
}
```

サブクラスのプロパティを`private`に変更すると、エラーになるのは変わらないがメッセージが上記とは異なる。

`Class 'classB' incorrectly extends base class 'classA'. Types have separate declarations of a private property 'hoge'.`

こっちは、「同じ`hoge`って名前で、スーパークラスとサブクラスで別々に宣言してるぞ（名前変えるか何とかしろよ）」というエラー。

```typescript
class classA {
    public hoge=1;

    constructor(){
        
    }
}

class classB extends classA {
    private hoge=2;

    constructor(){
        super();
    }   
}
```

逆に、スーパークラスで`public`、継承先で`private`にした場合はエラーメッセージが少し異なる。

`Class 'classB' incorrectly extends base class 'classA'. Property 'hoge' is private in type 'classB' but not in type 'classA'.`

「`hoge`っていうプロパティは**サブクラスのプライベート**だぞ、スーパークラスじゃないぞ」と、さっきの逆のことを言っている。ただ、どちらのパターンも継承元か継承先でプロパティが重複し、アクセス修飾子が異なるプロパティに対して再定義を行うと出現するエラー。

対処法はサブクラス側のプロパティ名を、スーパークラスとは異なる名前にすればいい。

```typescript
class classA {
    private hoge=1;

    constructor(){
        
    }
}

class classB extends classA {
    private hogehoge=2; // 名前を変更

    constructor(){
        super();
    }
}
```

単純にサブクラス側のプロパティ名を変更しただけだが、これだけでもエラーの解消は可能。

### reference

1. [クラス](https://nju33.com/notes/typescript/articles/%E3%82%AF%E3%83%A9%E3%82%B9)

## オブジェクトや配列から分割代入する

### detail

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

### reference

1. [分割代入](https://typescript-jp.gitbook.io/deep-dive/future-javascript/destructuring)
2. [残余引数](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Functions/rest_parameters)
