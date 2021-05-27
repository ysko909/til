# コンストラクタ引数にアクセス修飾子を付与してコードを簡略化する

## detail

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

## reference

1. [TypeScript Tooling in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-tooling-in-5-minutes.html)
1. [コンストラクタを定義する](https://typescript-jp.gitbook.io/deep-dive/future-javascript/classes#konsutorakutawosuru)
1. [コンストラクタの引数を使ってプロパティを宣言](https://future-architect.github.io/typescript-guide/class.html#id4)
1. [クラス宣言](https://docs.solab.jp/typescript/class/declaration/)
