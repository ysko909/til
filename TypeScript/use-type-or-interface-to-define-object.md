## TypeScriptでオブジェクトの型を定義するときは`interface`か`type`を使う

### detail

TypeScriptで、オブジェクトのプロパティについて型定義する方法は以下の通り。

```typescript
interface Person {
    firstName: string;
    lastName: string;
}
```

`interface`を使ってオブジェクトの名前を宣言し、プロパティの型をそれぞれ定義する。これと似たような機能を持つ`type`というものが存在する。

```typescript
type Person = {
    firstName: string;
    lastName: string;
}
```

`interface`がクラスやオブジェクトの**規格を定義する**のに対し、`type`は**型や型の組み合わせに名前をつけている**。見た目だけだとイコールが付いている以外はどっちも一緒じゃねーか、と思うが実際の挙動は多少異なる。

1. `interface`は宣言マージが可能。宣言マージとは、同じファイルやモジュール内に複数の同じ名前を持つ`interface`が存在した場合、自動的にまとめられて（マージされて）1つの`interface`として扱う機能。
2. `interface`は`extends`、つまり継承が可能。
3. `type`はあくまで型に名前をつける、という機能を提供しているに過ぎないので、比較的記述方法がバリエーションに富んでいる。Union型が宣言できるのも`type`の特徴。まあ、バリエーションに富んでいるというのは、裏を返すと選択肢が多いのでどれを選ぶか取捨選択が必要になる、という意味でもあるが。

他にもあるが大きな違いがこのあたり。じゃあ、どっちを使えばいいのだろうか。考え方はいくつかある。ただ、基本的には`type`を使ったほうがいいんじゃないだろうか。

1. 大前提として、チームでの開発ではチーム標準に準拠する。
2. 宣言マージが必要なら`interface`を選ぶ。
3. `type`は様々な型の定義ができるため、`interface`では記述できなくても`type`で統一が可能。

上記の例ではオブジェクトの型の組み合わせに名前をつけているだけなので、`interface`も`type`も対して違いはない。が、Union型などの宣言は`interface`で実装できない。よって、`type`を基本的に使って、どうしてもというシチュエーションでのみ`interface`を使う運用がいいと思われる。

### reference

1. [基本的な型付け](https://future-architect.github.io/typescript-guide/typing.html)
2. [【翻訳】TypeScriptにおけるInterfaceとType aliasの違い](https://freelance-jak.com/technology/typescript/1988/)
3. [【TypeScript】type の使いどころが知りたい話](https://mslgt.hatenablog.com/entry/2019/12/06/063526)
