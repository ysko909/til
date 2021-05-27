# 関数の引数にオブジェクトを指定する方法

## detail

関数の引数にオブジェクトを指定する場合の方法について記述する。

```typescript
const func = (arg:{x: string, y: string, z: string}): string => arg.x + arg.y + arg.z;

const func2 = (arg:{x: number, y: number, z: string}, arg2: string) => {
    return arg.x.toString() + arg.y.toString() + arg.z + arg2;
}

console.log(func({x:'foo', y: 'bar', z: 'baz'}));

console.log(func2({x: 1, y: 2, z: 'funcfunc'}, 'fugafuga'));
```

引数のオブジェクト自体に名前をつけ、各プロパティにそれぞれ型情報を付与する。引数を参照する場合は、`オブジェクトの名前.プロパティ名`で参照できる。このように、基本的には普段のTypeScriptの書き方で問題ない。

```typescript
type Hoge = {
    foo: number[];
    bar: string;
    baz: Function;
};

const sum = (numbers: number[]) => numbers.reduce((total, value) => total + value);

const func = (hoge: Hoge) => {
    console.log(sum(hoge.foo));
    console.log(hoge.bar.substr(3, 5));
    console.log(hoge.baz('hogehoge'));
}

let hoge:Hoge = {
    foo: [1, 2, 3, 4, 5],
    bar: 'hogefugapiyo',
    baz: (arg: string) => arg + ' in func'
}

func(hoge);

```

上記のように、`type`で定義したオブジェクトを引数に指定することも可能。

ちなみに`sum()`関数は、数値の配列の要素を合算する。案外、要素を合算するような配列のメソッドとかはないっぽい。

## reference

1. [Object types](https://www.typescriptlang.org/docs/handbook/2/objects.html)
2. [TypeScriptの関数を振り返る](https://qiita.com/pochopocho13/items/c92cba15be3f6a1b29a6)
