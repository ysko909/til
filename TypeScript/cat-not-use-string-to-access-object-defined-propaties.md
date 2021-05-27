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
    [key: string]: (number | string);
}

const obj1: Obj = {foo: 2, bar: 'ham'};

let key: string = 'foo';

const value = obj1[key];
```

正解は、上記のようにオブジェクトの**キーの型がわかるような**`type`を指定する必要がある。ただ、上記のように値が複数の型を取る場合、型判定がキーごとにできなくなってしまうデメリットがある。よって、上記の方法は「できなくはないがあまりやりたくない方法」となる。

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

上記の場合、オブジェクト側をいじるではなく**変数側をいじる**。`key`がstring型であることが原因なのだから、これをstringでない別モノにしてやればいい。別モノとは、「`type`で宣言したオブジェクトのキーのどれかですよー」と言ってやることを指す。

## reference

1. []()
