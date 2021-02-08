# TIL - TypeScript

TSに関することはここに書く。

## TypeScriptでオブジェクトの型を定義する方法

### detail

TypeScriptで、オブジェクトのプロパティについて型定義する方法は以下の通り。

```typescript
interface Person {
    firstName: string;
    lastName: string;
}
```

- `interface`を使ってオブジェクトの名前を宣言し、プロパティの型をそれぞれ定義する。
- これと似たような機能を持つ`type`というものが存在する。

```typescript
type Person = {
    firstName: string;
    lastName: string;
}
```

- `interface`がクラスやオブジェクトの**規格を定義する**のに対し、`type`は**型や型の組み合わせに名前をつけている**。
- 見た目だけだとイコールが付いている以外はどっちも一緒じゃねーか、と思うが実際の挙動は多少異なる。

1. `interface`は宣言マージが可能。宣言マージとは、同じファイルやモジュール内に複数の同じ名前を持つ`interface`が存在した場合、自動的にまとめられて（マージされて）1つの`interface`として扱う機能。
2. `interface`は`extends`、つまり継承が可能。
3. `type`はあくまで型に名前をつける、という機能を提供しているに過ぎないので、比較的記述方法がバリエーションに富んでいる。Union型が宣言できるのも`type`の特徴。まあ、バリエーションに富んでいるというのは、裏を返すと選択肢が多いのでどれを選ぶか取捨選択が必要になる、という意味でもあるが。

- 他にもあるが大きな違いがこのあたり。
- じゃあ、どっちを使えばいいのだろうか。考え方はいくつかある。ただ、基本的には`type`を使ったほうがいいんじゃないだろうか。

1. 大前提として、チームでの開発ではチーム標準に準拠する。
2. 宣言マージが必要なら`interface`を選ぶ。
3. `type`は様々な型の定義ができるため、`interface`では記述できなくても`type`で統一が可能。

- 上記の例ではオブジェクトの型の組み合わせに名前をつけているだけなので、`interface`も`type`も対して違いはない。が、Union型などの宣言は`interface`で実装できない。よって、`type`を基本的に使って、どうしてもというシチュエーションでのみ`interface`を使う運用がいいと思われる。

### reference

1. [基本的な型付け](https://future-architect.github.io/typescript-guide/typing.html)
2. [【翻訳】TypeScriptにおけるInterfaceとType aliasの違い](https://freelance-jak.com/technology/typescript/1988/)
3. [【TypeScript】type の使いどころが知りたい話](https://mslgt.hatenablog.com/entry/2019/12/06/063526)

## TypeScriptでのクラス宣言と継承について

TypeScriptでのクラス宣言と継承について、挙動をメモする。

```typescript
class Hoge {
  x: number;
  y: number;
  
  constructor(x: number, y: number){
    this.x = x;
    this.y = y;
  }
  
  add(z: number){
    return new Hoge(this.x + z, this.y + z);
  }
}

let hoge: Hoge = new Hoge(1, 2);
console.log(hoge);
console.log(hoge.add(3));
console.log(hoge.constructor.name);

class Fuga extends Hoge {
  xx: string;
  yy: string;
  constructor(x: number, y:number, xx: string, yy: string){
    super(x, y);
    this.xx = xx;
    this.yy = yy;
  }
  add_fuga(z: number, zz: string){
    return new Fuga(this.x + z, this.y + z, this.xx + zz, this.yy + zz);
  }
  add_fuga_super(z: number){
    return super.add(z);
  }
  
}

let fuga: Fuga = new Fuga(10, 11, 'foo', 'bar');
let fuga2: Fuga = fuga.add(9)
let fuga3: Fuga = fuga.add_fuga(99, 'baz');
let fuga4: Fuga = fuga.add_fuga_super(123);
console.log(fuga);
console.log(fuga2);
console.log(fuga3);
console.log(fuga4);
console.log(fuga.constructor.name);
console.log(fuga2.constructor.name);
console.log(fuga3.constructor.name);
console.log(fuga4.constructor.name);

class Piyo extends Hoge {
  x : string;
  constructor(y: number){
    super('string x', y);
  }
  add(y: number){
    return new Piyo(y);
  }
  
}

let piyo:Piyo = new Piyo(2222);
let piyo2:Piyo = piyo.add(9999);
console.log(piyo);
console.log(piyo2);
console.log(piyo.constructor.name);
console.log(piyo2.constructor.name);

```

- TypeScriptでのクラス宣言は、`class`で宣言しプロパティを型情報とともに記述する。
- 継承する場合`extends`で継承元のクラスを指定する。このとき、コンストラクタが存在する場合は`super()`で親コンストラクタを呼び出さないといけない。`super()`を読みだした後で、必要な処理を追加できる。

```console
Object {
  x: 1,
  y: 2
}
Object {
  x: 4,
  y: 5
}
"Hoge"
Object {
  x: 10,
  xx: "foo",
  y: 11,
  yy: "bar"
}
Object {
  x: 19,
  y: 20
}
Object {
  x: 109,
  xx: "foobaz",
  y: 110,
  yy: "barbaz"
}
Object {
  x: 133,
  y: 134
}
"Fuga"
"Hoge"
"Fuga"
"Hoge"
Object {
  x: "string x",
  y: 2222
}
Object {
  x: "string x",
  y: 9999
}
"Piyo"
"Piyo"
```

- 親クラスのメンバ関数を呼び出す場合、`super.`構文で呼び出せる。なお、上記のコードでは`Fuga`クラスにおいて親クラス`Hoge`のメンバ関数`add()`を呼び出す場合、**処理内容や戻り値は親クラスに記述されている内容に準拠する**。よって、`supeer.add()`を呼び出す`add_fuga_super()`の戻り値は、`Fuga`クラスではなく`Hoge`クラスであることに注意。
- ちなみに上記のコードはCodepenのTypeScript環境で実行している。`add_fuga_super()`の戻り値が`Hoge`クラスであるにもかかわらず、変数宣言時に`Fuga`クラスであると宣言しているが、これはエラーにならない。そういうものなのか・・・（`Fuga`を`Hoge`や‘Piyo`に書き換えてもエラーとかにはならなかったので、少なくともCodepenのTS環境はこういうものらしい）。
