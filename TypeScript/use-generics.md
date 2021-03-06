# ジェネリクスを使う

## detail

ジェネリクスはJavaなどにも搭載されている機能で、抽象的な型引数を利用して再利用が可能なクラスや関数などのコンポーネントを作成する機能。ジェネリクス自体は奥の深いもので、クラスの宣言にも利用できたり型の制限を設けたりと機能は多彩。ここでは、基本的な利用方法について記述する。

たとえば入力された値をそのまま返す、`echo`のような関数を考える。この関数からすると、引数がnumberなのかstringなのかは実際に呼び出されるまでわからないため、戻り値の型も呼び出されるまではわからない。このとき、単純に実装するなら`any`で型を指定する方法がある。

```typescript
function func(arg: any):any{
    return arg;
};

console.log(func('hoge'));
console.log(func(123));
```

これで、文字列でも数値でも受け取れる関数になる。ただし、戻り値は`any`となってしまい、関数の戻り値としての情報はないに等しい。本来は引数がstringなら戻り値もstring、引数がnumberなら戻り値もnumberとしてほしいのだが、`any`で指定しまうとこれらの欲しい情報が揮発してしまう。様々なデータ型を扱いつつ引数に対応した戻り値の型になるなど、再利用が可能な関数として宣言したい。こういう場合にジェネリクスが使える。

```typescript

function funcGen<T>(arg: T):T {
    return arg;
};

console.log(funcGen<string>('hoge'));
console.log(funcGen<number>(123));
```

ジェネリクスを使った関数宣言で記述した。ジェネリクスとして型を動的に指定するためには、関数名の直後に`<>`で**型引数**を指定することで実現する。このように型引数で型を指定すれば、引数だけでなく戻り値の型も指定できる。これで`any`を使って型を指定した時と異なり、戻り値の型情報が揮発することはない。

```typescript
console.log(funcGen('fuga'));
console.log(funcGen(987));
```

また、上記のように型を指定せずに関数を実行することも可能。この場合、コンパイラが引数の型を参照して自動的に型を判別し、戻り値の型にも反映してくれる。型の推論はコードを多少読みやすくしてくれるが、複雑なコードではコンパイラが型の推論に失敗しないとも限らず、そのような場合では明示的に型引数で指定する方が安全。

なお、アロー関数はfunctionで関数を宣言するのと記述形式が若干異なり、型引数に**カンマ**を記述する必要がある。

```typescript
function foo<T>(x: T): T { return x; } // 基本

const foo = <T>(x: T): T => x; // これはダメ。単純にアロー関数の形式で記述するとエラーになる

const foo = <T,>(x: T): T => x; // 型引数にカンマが必須
const foo = <T extends unknown>(x: T) => x; // あるいはextendsする
```

アロー関数でのジェネリクス記述時の特徴として、型引数にカンマを必ず記述する。これにより、アロー関数でもジェネリクスが書けるようになる。

このカンマについてだが、調べると「カンマなくても書ける」という記事と「カンマないとエラーになる」という記事が混在している。ただ、カンマのない書き方をした場合、[TypeScriptのPlayground](https://www.typescriptlang.org/play)では`JSX element 'T' has no corresponding closing tag.`というエラーになる。

ここで、エラーメッセージに含まれるJSXに気付いた。「なんでJSX？」と思ったら、どうもこれって**Reactに関連する現象**らしい。実際、さっきのエラーの他に`Cannot find name 'React'.`というエラーも同時に発生しており、なぜかReactを探しに行って「ないぞー」とエラーを吐いている。結論から言うと、ジェネリクスの文法`<>`がReact（というかJSX）の**タグとして誤って認識されている**のが原因で、「JSX使うならReact必要でしょ、でもReactないよ？」という解釈をコンパイラ側がしているために、前述のようなエラーを吐くことになる。実際はジェネリクスの文法だし、そもそもReactを使っていないのでこのエラーを解消するためには、ジェネリクスの文法であるカンマを書いて回避することになる。とりあえずカンマが1つ書いてあればジェネリクスとして認識されるようで、エラーは解消する。

そして、カンマなしでOKというのは`*.ts`ファイルに書く場合のようだ。つまり、ファイルの拡張子からしてTypeScriptであると確認できる場合はJSXだと誤認しないので、コンパイラが`<>`の文法をジェネリクスだと正しく判断してくれる。

ちなみにだが、カンマとは別の解消法がある。それは、`extends`を使って型引数を制限してしまう方法だ。

```typescript
const hoge = <T extends unknown>(arg: T) => arg;

console.log(hoge(123));
console.log(hoge('123abc'));
```

JSXのタグには`extends`という文法はないため、ジェネリクスとして扱われる。難点を上げるならば、`extends`を記述する場合は当たり前だが**制限する型を何かしら記述する必要がある**。ここで`string`とか`number`とか具体的な型で制限してしまうと、その型しか受け付けなくなってしまいジェネリクスの意味を成さない。そのため、**型引数の制限は記述するけど実質的に制限しない**という、一見不思議な状況を作る必要がある。この状況を成立させられるのが`unknown`という型。TypeScriptの仕様上、すべての型は`unknown`を継承して実装されているため、型引数`T`の制限を**実質的に制限しない**ものとして扱うことが可能になる。

とは言え、これだけだとイマイチ実際に使えるシチュエーションが思い浮かばないと思うので、別のユースケースを考えてみる。

たとえば複数のクラスが存在しており、それぞれのクラスについて配列があるとしよう。

```typescript
type Person = {
    id: number;
    name: string;
}

type Dog = {
    id: number;
    type: string;
}

type Company = {
    id: number;
    name: string;
    phoneNumber: string;
}

let hogePerson: Person[] = [
    {id: 1, name: 'foo'},
    {id: 2, name: 'bar'},
    {id: 3, name: 'baz'},
    {id: 4, name: 'foofoo'},
];

let hogeDog: Dog[] = [
    {id: 10, type: 'Labrador Retriever'},
    {id: 11, type: 'German Shepherd'},
    {id: 12, type: 'French Bulldog'}
];

let hogeCompany: Company[] = [
    {id: 100, name: 'Apple.Inc', phoneNumber: '012-345-6789'},
];

```

このクラスの配列に、要素を追加するような関数を作りたい。しかもただ、追加するだけでなく、プロパティ`id`が重複した場合は、新しい要素を基準にして`id`を採番しなおしてから追加したい。こういう場合、クラスごとに関数をそれぞれ別個に作成するわけにはいかない。クラスが100個あったら、それぞれにクラス用に関数を100個作るのか？という話になってしまうからだ。機能は「配列に要素を追加する」という共通した機能なのだから、入出力データの型さえ抽象化できれば1つの関数を流用できる。こういうシチュエーションで威力を発揮するのがジェネリクス、というわけだ。

```typescript
type Person = {
    id: number;
    name: string;
}

type Dog = {
    id: number;
    type: string;
}

type Company = {
    id: number;
    name: string;
    phoneNumber: string;
}

type Obj = {
    id: number;
}

const addItem = <T extends Obj>(array: T[], newItem: T): T[] => {
    const idx = array.findIndex(item => item.id === newItem.id);
    if (idx === -1){
        return [
            ...array,
            newItem
        ];
    } else {
        return [
            ...array.slice(0, idx),
            newItem,
            ...array.slice(idx).map(item => {
                item.id += 1;
                return item;
            })
        ];
    }
};

let hogePerson: Person[] = [
    {id: 1, name: 'foo'},
    {id: 2, name: 'bar'},
    {id: 3, name: 'baz'},
    {id: 4, name: 'foofoo'},
];

console.log(addItem<Person>(hogePerson, {id:3, name:'addItem'}));

let hogeDog: Dog[] = [
    {id: 10, type: 'Labrador Retriever'},
    {id: 11, type: 'German Shepherd'},
    {id: 12, type: 'French Bulldog'}
];

console.log(addItem<Dog>(hogeDog, {id: 9, type: 'Beagle'}));

let hogeCompany: Company[] = [
    {id: 100, name: 'Apple.Inc', phoneNumber: '012-345-6789'},
];

console.log(addItem<Company>(hogeCompany, {id: 100, name: 'Amazon.Inc', phoneNumber: '999-999-9999'}));

```

実際のコードは上記のようになる。

```console

[LOG]: [{
  "id": 1,
  "name": "foo"
}, {
  "id": 2,
  "name": "bar"
}, {
  "id": 3,
  "name": "addItem"
}, {
  "id": 4,
  "name": "baz"
}, {
  "id": 5,
  "name": "foofoo"
}] 
[LOG]: [{
  "id": 10,
  "type": "Labrador Retriever"
}, {
  "id": 11,
  "type": "German Shepherd"
}, {
  "id": 12,
  "type": "French Bulldog"
}, {
  "id": 9,
  "type": "Beagle"
}] 
[LOG]: [{
  "id": 100,
  "name": "Amazon.Inc",
  "phoneNumber": "999-999-9999"
}, {
  "id": 101,
  "name": "Apple.Inc",
  "phoneNumber": "012-345-6789"
}] 
```

実行結果はこんな感じ。入出力データのクラスに関係なく、重複していれば`id`を採番し直しつつ要素を追加できた。

このように、「入出力のデータ型が異なるだけで機能は共通」という場合は、ジェネリクスを使って機能を1つに共通化できる。

## reference

1. [【TypeScript】Generics(ジェネリックス)を理解する](https://qiita.com/k-penguin-sato/items/9baa959e8919157afcd4)
2. [ジェネリクス](http://js.studio-kingdom.com/typescript/handbook/generics)
3. [What is the syntax for Typescript arrow functions with generics?](https://stackoverflow.com/questions/32308370/what-is-the-syntax-for-typescript-arrow-functions-with-generics)
4. [【TypeScript】.tsx内でGeneric関数を定義する場合の注意点](https://marsquai.com/745ca65e-e38b-4a8e-8d59-55421be50f7e/1f67fdab-8e00-4ae1-a1b9-077d5a30a5d6/daf351d1-ce44-4bbe-9631-377326e2e43e/)
5. [TypeScriptでジェネリクス(Generics)を理解するための簡単なチュートリアル](https://blog.mitsuruog.info/2019/03/try-typescript-generics-101)
