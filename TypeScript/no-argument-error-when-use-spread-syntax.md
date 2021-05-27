# 配列をスプレッド構文で展開し引数に指定すると引数なしエラーになる

## detail

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

## reference

1. [More on functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
2. [【TypeScript】as const ( const assertion )](https://uga-box.hatenablog.com/entry/2020/09/19/000000)
