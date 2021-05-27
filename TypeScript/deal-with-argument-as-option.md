# 関数の引数をオプションにする方法

## detail

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

## reference

1. [More on functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
2. [TypeScript プログラミング -関数 -引数](https://docs.solab.jp/typescript/function/parameter/)
