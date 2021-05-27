# アクセサを記述する

## detail

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

## reference

1. [Classes](https://www.typescriptlang.org/docs/handbook/2/classes.html#getters--setters)
1. [クラス](http://js.studio-kingdom.com/typescript/handbook/classes#accessors)
