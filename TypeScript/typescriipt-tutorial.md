# TypeScriptのチュートリアルをやってみる

## detail

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

## reference

1. [TypeScript Tooling in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-tooling-in-5-minutes.html)
1. [コンストラクタを定義する](https://typescript-jp.gitbook.io/deep-dive/future-javascript/classes#konsutorakutawosuru)
1. [コンストラクタの引数を使ってプロパティを宣言](https://future-architect.github.io/typescript-guide/class.html#id4)
1. [クラス宣言](https://docs.solab.jp/typescript/class/declaration/)
2. [Typescript: TS7006: Parameter 'xxx' implicitly has an 'any' type](https://stackoverflow.com/questions/43064221/typescript-ts7006-parameter-xxx-implicitly-has-an-any-type)
