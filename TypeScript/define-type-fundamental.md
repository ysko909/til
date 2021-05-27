## 型宣言いろいろ

### detail

TypeScriptは変数や関数の引数、戻り値など様々な場面でその値の型を表現する。プリミティブ型については、たとえば`let hoge: string;`のように記述したりする。プリミティブ型については比較的見た目がわかりやすいのだが、オブジェクト型リテラルになると記述方法が多彩で複雑なので、ここで整理しておく。もちろん、これ以外の記述方法もあると思うが、普段利用頻度の高い主だったところはこんなところではないだろうか。

```typescript
type TypeSample = {
    // なんでもあり
    anything: any;
    
    // プリミティブ型
    str: string;
    num: number;
    bool: boolean;
    
    // ユニオン型
    strOrNum: string | number;

    // 以下は全てオブジェクト型リテラル
    arrayNum : number[]; // 数値の配列
    arrayStr : string[]; // 文字列の配列
    arrayBool : boolean[]; // 真偽値の配列
    arraAny : any[]; // なんでもありの配列
    arrayObj : {[key:string]: (number | string)}[]; // キー名が文字列で数値あるいは文字列の値を持つオブジェクトの配列
    multiArray : any[][]; // なんでもありの2次元配列

    obj: {key: any}; // キー名が`key`で値がなんでもありであるオブジェクト
    arrayInObj: {key: any[]}; // キー名が`key`で値がなんでもありな配列であるオブジェクト
    multiArrayInObj: {key: any[][]}; // キー名が`key`で値がなんでもありな2次元配列であるオブジェクト
    stringKey: {[key: string]: any}; // キー名が文字列で値がなんでもありであるオブジェクト
    numberKey: {[key: number]: any}; // キー名が数値で値がなんでもありであるオブジェクト
    stringKeyAndArrayInObj: {[key: string]: (number | string)[]}; // キー名が文字列で値が数値か文字列の配列であるオブジェクト
    stringKeyAndArrayInObjPart2: {[key: string]: number[] | string}; // キー名が文字列で値が「数値のみの配列」か文字列であるオブジェクト

}

let sample : TypeSample = {
    anything: [1, 2, 'a'],

    str: 'hoge',
    num: 999,
    bool: false,
    strOrNum: 'foo',

    arrayNum : [1, 2, 3, 4, 5],
    arrayStr : ['foo', 'bar', 'baz'],
    arrayBool : [true, false, false],
    arraAny : [{'hoge': 1}, true, 'foo', 123],
    arrayObj : [{'ham': 1}, {'eggs': 'foo', 'spam': 321}, {'foo':1, 'bar': 2, 'baz': 'bazbazbaz'}],

    multiArray : [[1, 2, 'hoge'], ['fuga', 3, 'piyo'], ['ham', 'eggs', 'spam', `python`]],

    obj: {key: 'hoge'},
    arrayInObj: {key: [true, [0, 1, 2, 3], {'hoge': 1, 2: 'fuga'}, 999, 'piyo']},
    multiArrayInObj: {key: [['ham', 1, {'foo': [9, 8, 7]}, 2], ['eggs', true], [false, 10, 'spam', [3, 2, 1, 0]]]},
    stringKey: {'hoge': [1, 2, 3], 'fuga': true, 'piyo': {1: 'foo'}},
    numberKey: {1: false, 2: [3, 2, 1], 999: {'hoge': 'fuga'}},
    stringKeyAndArrayInObj: {'ham': [1, 2, 3], 'eggs': ['hoge', 1, 'fuga', 2, 'piyo'], 'spam': ['foo', 'bar', 'baz']}
    stringKeyAndArrayInObjPart2: {'ham': [1, 2, 3], 'eggs': 'hoge', 'spam': 'foo'}
}
```

ユニオン型で定義する際、`(number | string)[]`と`number[] | string[]`は意味合いが異なることに注意。前者は「数値あるいは文字列を要素に含んだ配列」だが、後者は「数値のみを含んだ配列、または文字列のみを含んだ配列」を指す。

### reference

1. [Everyday types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
1. [TypeScript のオブジェクト型リテラルいろいろ](https://qiita.com/hida/items/fb0379353bbb71f8191b)
