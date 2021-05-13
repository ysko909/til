# 関数の引数に再代入することの可否について

## detail

Airbnbのコーディング規約には、関数の引数へ値を再代入することは禁止されているらしい。

> 7.13 パラメータを再割り当てしない。eslint: no-param-reassign

```javascript
const hoge = foo => {
  foo += 1;
  return foo;
}

hoge(1);
```

上記のように、`foo`に再代入するのはダメということになる。

ESLintなどでは、「no-param-reassign」という名前で[ルール](https://eslint.org/docs/rules/no-param-reassign)が存在している。実際、ESLintを体験できる[デモサイト](https://eslint.org/demo)でこのルールを有効にした状態で上記のコードを記述すると、再代入している部分でエラーメッセージが表示される。ただ、このルールはデフォルトでOFFになっている。そこまで優先度は高くないのだろうか。プロジェクトの思想や規約に多少左右されることではあるかもしれないが。

```javascript
const hoge = (foo, bar) => {
  foo = 9;
  bar.push(4);
  console.log(foo.toString() + ' : ' + bar.toString());
}

let n = 1;
let a = [0, 1, 2, 3];

console.log(n.toString() + ' : ' + a.toString());
hoge(n, a);
console.log(n.toString() + ' : ' + a.toString());

```

個人的には、上記のような再代入を行うコードが危ないと思っている。

```console
1 : 0,1,2,3
9 : 0,1,2,3,4
1 : 0,1,2,3,4
```

実行してみると、変数`n`と`a`で比較したとき関数`hoge`を実行する前後で`n`は変化しないものの、配列である`a`は内容が変化してしまっている。関数内で引数に対し操作を行ってしまったため、もともと変数に格納されていた値と乖離してしまうパターンだ。ただ、これは再代入処理が悪いというよりミュータブルとイミュータブルを適切に処理していないために起きている症状なので、再代入の是非を問うケースでは少々例えが悪い気がする。

まぁ、このようなコードを書いてしまい、オブジェクトを書き換えてバグの発生原因を作ってしまうことが危惧されるくらいのコードを書く可能性があるなら、ルールで禁止してしまえということも1つの解決策かもしれない。

## reference

1. [7.13 Never reassign parameters. eslint: no-param-reassign](https://mitsuruog.github.io/javascript-style-guide/#functions--reassign-params)
2. [JavaScriptの仮引数に再代入することの是非](https://teratail.com/questions/173485)
3. [「Airbnb JavaScript Style Guide」の引数への再代入禁止ルール](https://teratail.com/questions/173503)
4. [Why can a function modify some arguments as perceived by the caller, but not others?](https://stackoverflow.com/questions/575196/why-can-a-function-modify-some-arguments-as-perceived-by-the-caller-but-not-oth)
5. [JavaScriptでイミュータブルなプログラミングをする](https://sbfl.net/blog/2018/09/25/javascript-immutable-programming/)
