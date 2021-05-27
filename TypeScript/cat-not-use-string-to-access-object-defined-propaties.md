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

## reference

1. []()
