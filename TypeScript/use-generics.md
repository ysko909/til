# ジェネリクスを使う

## detail

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

## reference

1. [【TypeScript】Generics(ジェネリックス)を理解する](https://qiita.com/k-penguin-sato/items/9baa959e8919157afcd4)
2. [ジェネリクス](http://js.studio-kingdom.com/typescript/handbook/generics)
3. [What is the syntax for Typescript arrow functions with generics?](https://stackoverflow.com/questions/32308370/what-is-the-syntax-for-typescript-arrow-functions-with-generics)
