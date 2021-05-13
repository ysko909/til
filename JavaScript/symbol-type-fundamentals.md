# Symbol型とはプリミティブな唯一無二のIDを生成する

## detail

symbol型はプリミティブなデータ型で、`Symbol()`関数はsymbol型の値を返す。このsymbol型のデータは必ずユニークなIDであり、文字列や数値のようなデータとは扱いが異なる。また、コンストラクターとしての機能を持たないので、`new Symbol()`と書くとエラーになる。

```typescript
const hoge = Symbol();

console.log(hoge === Symbol()); // false
console.log(Symbol() === Symbol()); // false
console.log(hoge === hoge); // true

const fuga = Symbol('fuga');

console.log(fuga === Symbol('fuga')); // false
console.log(Symbol('fuga') === Symbol('fuga')); // false
console.log(hoge === fuga); // false
console.log(fuga === fuga); // true
```

`Symbol() === Symbol()`が**false**になるのが特徴的で、これがsymbol型たる所以。つまり1番目の`Symbol()`と2番目の`Symbol()`は、コード上の見た目こそ同じであるものの、返す値が異なっていることがわかる。`===`で比較してtrueになるようなものを、後で`Symbol()`を使って生成できないのがsymbol型。じゃあ実際に返ってきている値を見られるかというと、これは**無理**。`console.log(Symbol('piyo'));`と実行しても`Symbol(piyo)`と返ってくるだけで、実際の値そのものが見られるわけではない。これに関しては、こういう仕様だとあきらめるしかない。

```typescript
const hoge = Symbol();
const obj = {
    [hoge] : 'foo',
};

const objAlt = {
    hoge: 'bar',
};

console.log(obj[hoge]); // 'foo'
console.log(obj.hoge); // undefined
console.log(obj['hoge']); // undefined

console.log(objAlt[hoge]); // undefined
console.log(objAlt.hoge); // 'bar'
console.log(objAlt['hoge']); // 'bar'

const fuga = hoge;

console.log(hoge === fuga); true
console.log(obj[fuga]); // 'foo'
console.log(obj[hoge] === obj[fuga]); // true
```

`Symbol()`で生成したIDは、オブジェクトのキーに設定できる。このとき、プラケット（`[]`）を記述するのとしないのとで、生成したオブジェクトの挙動が変わるので注意が必要。プラケットを付与しなかった場合、それは`hoge`という文字列でキーが作成されるため、`objAlt.hoge`や`objAlt['hoge']`という記法でアクセスが可能。逆にこれ以外の記法だと結果が`undefined`になる。

対して、symbol型によるキー設定はプラケットを付与した場合のみ有効となるため、プラケットを付与したオブジェクトのキーにアクセスする場合は`obj[hoge]`と記述する必要がある。これ以外の記法、たとえばピリオド使いでプロパティにアクセスしようとすると結果は`undefined`になる。

このsymbol型を有効活用しようとするなら、「ビルトインクラスに対し安全に拡張を行える」などのメリットはあるもののそこまで有用かと言われると正直微妙。どちらかというとJSの互換性保持のために実装されたという側面があり、既存のライブラリについて動作を（ある程度）保証しつつも言語拡張を行う策と考えられる。つまり、JavaScript（というかECMAScript）の仕様策定側には必須だが、一般的な開発者にとってはそこまで有用というほどでもない。

ただ、こういう唯一無二の仕様を持つため、今後実装される別機能と組み合わせると途端に有用にならないとも言えず、一応でもその機能と挙動を理解しておくに越したことはない。

## reference

1. [Symbol](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
2. [JavaScript互換性の要！Symbol（シンボル）について](https://analogic.jp/symbol/)
3. [ECMAScript6にシンボルができた理由](https://qiita.com/naruto/items/312adeb6145eb6221be7)
4. [Symbolについて](http://js-next.hatenablog.com/entry/2014/03/01/001650)
