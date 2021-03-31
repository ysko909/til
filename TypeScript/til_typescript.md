# TIL - TypeScript

TSに関することはここに書く。

## TypeScriptでオブジェクトの型を定義する方法

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

## TypeScriptのfunction型について

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

### reference

1. [クラス](https://typescript-jp.gitbook.io/deep-dive/future-javascript/classes)
1. [クラス](https://docs.solab.jp/typescript/class/inheritance/)
1. [JSのクラス名をゲットする](https://qiita.com/makerbox/items/3f6ed31abbb128f5edb1)
1. [JavaScriptの「型」の判定について](https://qiita.com/south37/items/c8d20a069fcbfe4fce85)

## 型定義ファイルがないよと言われたら

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

## インデックスシグネチャについて

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

なお、連想配列はもともとなかったキーと値を新たに設定することができる。「もともと存在するキーfooの値を書き換えようとして、タイポしたキーfoaに値を設定してしまう」という可能性はまあまあ考えられる。その場合は新しい要素として追加される。

### reference

1. [第9回　連想配列の取り扱い方](https://www.atmarkit.co.jp/ait/articles/1501/29/news117_4.html)
2. [Index signature（インデックス型）](https://typescript-jp.gitbook.io/deep-dive/type-system/index-signatures)
3. [TypeScript：インデックスシグネチャが型安全を破壊する例](https://qiita.com/aakasaka/items/0b081c90b1b99b82143c)

## TypeScriptでの型指定について

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
