## `Property xxx has no initializer and is not definitely assigned in the constructor`エラーは初期値を代入するか`!`を付与する

### detail

自分オリジナルのクラスを用いたコードを実行する際、`Property xxx has no initializer and is not definitely assigned in the constructor`というエラーが表示されることがある。`xxx`の部分はプロパティ名が入っており、「このプロパティ、初期化されてないしコンストラクタの中でも代入されてないぞ」と言っている。

```typescript
class C {
  foo: number;
  bar = "hello";
  baz: boolean;
  //  ~~~
  //  Error! Property 'baz' has no initializer and is not definitely assigned in the
  //         constructor.
  constructor() {
    this.foo = 42;
  }
}
```

上記だと`baz`というプロパティについてエラーが発生しており、「`baz`ってプロパティが初期化されてないぞ」と言っている。実際、エラーの表示されていないプロパティの`foo`はコンストラクタ内で値が設定されているし、`bar`は宣言時に初期値が代入されている。`baz`もどちらかの方法で初期値を設定すれば、このエラーを解消できる。

```typescript
class C {
  foo: number;
  bar = "hello";
  baz: boolean;
  
  constructor() {
    this.foo = 42;
    this.baz = false;
  }
}

```

コンストラクタ内で初期値を設定するなら、上記のように書けばいい。これでエラーは解消できる。

```typescript
class C {
  foo: number;
  bar = "hello";
  baz = true;
  
  constructor() {
    this.foo = 42;
  }
}

```

プロパティ宣言時に初期値を設定する方法でも、エラーは解消できる。どちらでも好きな方法で書けばいい。

```typescript
class C {
  foo: number;
  bar = "hello";
  baz!: boolean; // プロパティ名の後に！をつける
  
  constructor() {
    this.foo = 42;
  }
}
```

他の解消方法として、エラーが起こっている対象のプロパティに`!`を付与する方法がある。この`!`は「[Non-null assertion operator](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#non-null-assertion-operator)」と言われるもの。プログラマーがコンパイラに対して、「この項目はNullやらundefinedにはならんぞ、だから文句（エラー）言うな」と教える機能がある。上記の場合で言うと`baz`はbooleanであるものの、初期値が設定されていないためNullやundefinedの可能性がある（実際、コード上初期値は設定されていない）。このとき`baz`の型は実態が`boolean | undefined`となっているため、booleanとして指定されているコードと実態が一致しなくなっている。そこで、「このプロパティはNullやundefinedにはならんぞ」をコンパイラに教えてやることで、`boolean | undefined`を`boolean`にキャストし、コード上指定している型と実態の型を合わせている。

ちなみに、TypeScriptはプロパティについて宣言時またはコンストラクタ内において、プロパティに初期値を設定することを強制するフラグが存在する。`strictPropertyInitialization`のことだが、これが有効である場合は初期値の設定を強制する。つまり、このフラグを無効にすると、上記のエラーは出現しなくなる。ただし、値の設定忘れやタイプミスなどを防止するためにの、このフラグは有効にしておくべきだと思う。

### reference

1. [Non-null assertion operator](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#non-null-assertion-operator)
2. [TypeScript complain “has no initializer and is not definitely assigned in the constructor” about constructors by returning constructed object](https://stackoverflow.com/questions/54104187/typescript-complain-has-no-initializer-and-is-not-definitely-assigned-in-the-co)
3. [TypeScriptの変数の末尾の"!"(エクスクラメーション/感嘆符)の意味](https://qiita.com/zigenin/items/364264a6cf635b962542)
4. [Strict Class Initialization](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-7.html#strict-class-initialization)
5. [In Typescript, what is the ! (exclamation mark / bang) operator when dereferencing a member?](https://stackoverflow.com/questions/42273853/in-typescript-what-is-the-exclamation-mark-bang-operator-when-dereferenci)
