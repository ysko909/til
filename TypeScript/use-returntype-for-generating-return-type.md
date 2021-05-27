# ReturnTypeを使って関数の戻り値の型を生成する方法

## detail

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

## reference

1. [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
2. [Object Types](https://www.typescriptlang.org/docs/handbook/2/objects.html)
3. [【TypeScript】Utility TypesのReturnTypeを深く理解する](https://qiita.com/NOMURA_keibyou38/items/fc9d21aee217b4213430)
4. [【TypeScript】型定義で関数の戻り値を直接使えるReturnType](https://uiuifree.com/blog/develop/type-script-return-type/)
