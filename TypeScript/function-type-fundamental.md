
# TypeScriptには「function型」という型がある

## detail

```typescript
interface Props{
  width?: number;
  height?: number;
  onClick?: Function;
}
```

とあるところで、上記のようなコードを見かけた。上の2つはわかるとして、`function`って型を指定できるの？って話だが、結論から言えばTypeScriptは**できる**。

```typescript
// a, bは任意の値
// 2つの引数を受け取ってnumber型を返すことを示しており、このような構造の
// functionしか代入できない
  onClick?: (a: number, b: number) => number;
```

代入される関数の引数と戻り値の型が決まっている場合は、上記のように記述した方がよい。functionの内容を詳細に記述することで、余計な代入時にチェックが働くようになる。

## reference

1. [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
1. [TypeScript ~基礎編~ 型](https://selfnote.work/20200726/programming/typescript-basic-type/)
