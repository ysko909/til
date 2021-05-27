# Rest引数の使い方

## detail

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

## reference

1. [More on functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
2. [TypeScript プログラミング -関数 -引数](https://docs.solab.jp/typescript/function/parameter/)
