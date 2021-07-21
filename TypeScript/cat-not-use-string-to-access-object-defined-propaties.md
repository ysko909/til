# string型を利用して定義済みオブジェクトのプロパティにアクセスすることはできない

## detail

```console
Element implicitly has an 'any' type because expression of type 'string' can't be used to index type 'Obj'.
No index signature with a parameter of type 'string' was found on type 'Obj'.
```

とあるコードを書いている際、上記のようなエラーが発生した。

単純に直訳すると、「型'Obj'のインデックスに型'string'の式を使用できないため、要素は暗黙的に'any'型を持っています。型'Obj'には、'string'型のパラメータを持つインデックス署名が見つかりませんでした。」となる。

```typescript

const obj1 = {foo: 2, bar: 'ham'};

let key: string = 'foo';

const value = obj1[key]; // ここの部分でエラー
```

上記のようなコードを書いているとき、`obj1[key]`の部分でエラーが表示される。

```typescript
type Obj = {
    foo: number;
    bar: string;
    baz?: string;
}

const obj1: Obj = {foo: 2, bar: 'ham'};

let key: string = 'foo';

const value = obj1[key]; // 相変わらずエラー
```

`type`でオブジェクトのプロパティについて型を定義しても、エラーは消えなかった。

```typescript
type Obj = {
    foo: number;
    bar: string;
    [key: string]: (number | string);
};

const obj1: Obj = {foo: 2, bar: 'ham'};

let key: string = 'foo';

const value = obj1[key];
```

正解は、上記のようにオブジェクトの**キーの型がわかるような**`type`を指定する必要がある。ただ、上記のように値が複数の型を取る場合、型判定がキーごとにできなくなってしまうデメリットがある。上記で言うと、プロパティ`foo`と`bar`については型が指定されているため、これらの型チェックは働く。ところが未知の`baz`というプロパティが指定された場合は、「値の型がstringかnumberのいずれか」になってしまう。`false`などの値はチェックできるものの、上記の方法は「できなくはないが積極的にやりたいかと言われると微妙」な方法であると言える。

じゃあどうするか。

```typescript
type Obj = {
    foo: number;
    bar: string;
    baz?: string;
}

const obj1: Obj = {foo: 2, bar: 'ham'};

let key: keyof Obj = 'foo';

const value = obj1[key];

```

上記の場合、オブジェクト側をいじるではなく**変数側をいじる**。`key`がstring型であることが原因なのだから、これをstringでない別モノにしてやればいい。別モノとは、「`type`で宣言したオブジェクトのキー（のどれか）ですよー」と言ってやることを指す。そうすれば、キーごとに型が違う場合は個別に宣言ができるので、型チェックは従来通り正常に動作する。可能であれば、この方法がいいと思われる。

ただ、上記の方法にも問題がある。それは`keyof`の対象に**定義済みのオブジェクトを指定できない**ということだ。

```typescript
const obj1 = {foo: 2, bar: 'ham'};

let key: keyof obj1 = 'foo'; // これはエラーになる。

const value = obj1[key];
```

上記のように、定義済みオブジェクトを`keyof`構文に対し指定することでできない。`'obj1' refers to a value, but is being used as a type here. Did you mean 'typeof obj1'?`と言われる。無論、`typeof`のタイポなどではなく、単純に定義済みオブジェクトが`keyof`の対象外ってだけだ。

`keyof`はTypeScript独自の構文なのだが、`keyof`が参照する先は`type`あるいは`interface`で宣言したものであり、オブジェクトそのものではない。つまり、`keyof`は宣言済みの定義に基づいているので、定義済みオブジェクトの内容を参照してデータ型を逆算したり推測したりしているわけではない。そのため、オブジェクトが定義済みである場合はこの方法を利用できず、1つ前の`[key: string]: any;`などの形式で宣言した`type`を使い`keyof`するしかない。

```typescript
type Obj = {
    foo: number;
    bar: string;
    [key: string]: (number | string);
};

const obj1: Obj = {foo: 2, bar: 'ham'};

let key: keyof Obj = 'foo';

const value = obj1[key];
```

一応、上記のようにすればエラーが発生することはないし、`[key: string]: any;`だけでなく普段通り`キー名: 値の型`で宣言しておけば、宣言済みのキーに関しては型チェックが働く。まぁ、既存以外のプロパティについては型チェックが働かないってのは今まで通りなのだが。

## reference

1. [Typescript ブラケット記法(Object[key])でno index signatureエラーをtype safeに解決したい。](https://aknow2.hatenablog.com/entry/2018/11/14/143033)
2. [Object等の呼び出しでkeyに動的な変数を使う [TypeScript]](https://qiita.com/tktcorporation/items/051bb03bb4d72930d8e9)
3. [Keyof Type Operator](https://www.typescriptlang.org/docs/handbook/2/keyof-types.html)
