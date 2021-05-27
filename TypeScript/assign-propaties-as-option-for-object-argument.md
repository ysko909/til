# オブジェクトを引数にしたときのオプション指定はプロパティごとに可能

## detail

オブジェクトを関数の引数に指定するとき、プロパティの一部をオプションにしたい場合は、`?`を付与することでプロパティごとにオプション扱いできる。

```typescript
const func=(arg:{hoge: number, fuga: string, piyo: number[]})=>{
    console.log(arg.hoge);
    console.log(arg.fuga);
    console.log(arg.piyo);
};

func({
    hoge: 1,
    fuga: 'foo',
    // Argument of type '{ hoge: number; fuga: string; }' is not assignable to parameter of type '{ hoge: number; fuga: string; piyo: number[]; }'.
    // Property 'piyo' is missing in type '{ hoge: number; fuga: string; }' but required in type '{ hoge: number; fuga: string; piyo: number[]; }'.(2345)
});
```

上記のように、全プロパティが必須であるオブジェクトを引数にとる関数`func`があったとき、あるプロパティを省略すると当たり前だがエラーになる。

```typescript
const func=(arg:{hoge: number, fuga: string, piyo?: number[]})=>{
    console.log(arg.hoge);
    console.log(arg.fuga);
    console.log(arg.piyo);
};

func({
    hoge: 1,
    fuga: 'foo',
});
```

上記のようにオプション扱いしたいプロパティに`?`を付与することで、そのプロパティを省略可能とする。

```console
1 
"foo" 
undefined 
```

なお、オプション扱いしているのでプロパティ`piyo`に値はセットされていない。その状態で上記のコードは実行すると、当たり前だが`undefined`になるので注意が必要。

```typescript

type Hoge = {
    foo: number[];
    bar: string;
    baz: Function;
};

const sum = (numbers: number[]) => numbers.reduce((total, value) => total + value);

const func = (hoge: Hoge) => {
    const {
        foo = [100, 200, 300],
        bar= 'init',
        baz=(arg: string)=>'init: ' + arg
    } = hoge;
    
    console.log(sum(foo));
    console.log(bar.substr(3, 5));
    console.log(baz('hogehoge'));
}

let hoge:Hoge = {
    foo: [1, 2, 3, 4, 5],
    bar: 'hogefugapiyo',
    // baz: (arg: string) => arg + ' in func'
    // Property 'baz' is missing in type '{ foo: number[]; bar: string; }' but required in type 'Hoge'.
}

func(hoge);
```

`type`で引数のオブジェクトの内容を指定する方法でも同じ。全プロパティを必須扱いすると、関数実行時にプロパティの指定がないとエラーになる。

```typescript

type Hoge = {
    foo: number[];
    bar: string;
    baz?: Function; // ?を付与した
};

const sum = (numbers: number[]) => numbers.reduce((total, value) => total + value);

const func = (hoge: Hoge) => {
    const {
        foo = [100, 200, 300],
        bar= 'init',
        baz=(arg: string)=>'init: ' + arg
    } = hoge;
    
    console.log(sum(foo));
    console.log(bar.substr(3, 5));
    console.log(baz('hogehoge'));
}

let hoge:Hoge = {
    foo: [1, 2, 3, 4, 5],
    bar: 'hogefugapiyo',
}

func(hoge);
```

オプション扱いしてやれば、該当するプロパティに関しては省略してもエラーにならない。

## reference

1. [TypeScript の Optional Property](https://ema-hiro.hatenablog.com/entry/2019/01/28/013325)
