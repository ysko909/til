# 関数の引数にデフォルト値を設定する方法

## detail

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

## reference

1. [More on functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
2. [TypeScript プログラミング -関数 -引数](https://docs.solab.jp/typescript/function/parameter/)
