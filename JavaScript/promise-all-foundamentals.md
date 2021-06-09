# `Promise`を使って複数の非同期処理同時に行う`all`

## detail

`Promise`を使って非同期処理を実行するとき、複数の非同期処理を一度に行いたいとする。こういうときは`all`を使うと、実装が楽になる。

```typescript
const getBool = (num = 0.5) => {
  return Math.random() > num;
}

const makePromise = (waitMin = 1000, idx = 0): Promise<any> => {
  return new Promise((resolve: Function, reject: Function) => {
    setTimeout(()=>{
      if(getBool()){
        console.log('resolve :' + idx.toString());
        resolve();
      }
      else {
        console.log('reject :' + idx.toString());
        reject();
      }      
    }, waitMin);
  });
}

let promiseArray:Promise<void>[] = [];

[2000, 500, 1200].forEach((val, idx)=>{
  promiseArray.push(makePromise(val, idx));
})

console.log('proccess start.');

Promise.all(promiseArray).then(()=>{
  console.log('resolve.');
}).catch(()=>{
  console.log('reject.');
}).finally(()=>{
  console.log('finally.');
});

```

相変わらず上記はTypeScriptで記述しているが、JavaScriptでも似たようなものだと思う。

関数`getBool()`はランダムでTrueあるいはFalseを返す。この値に基づいて、`Promise`は解決か拒否かを判断している。

複数の`Promise`オブジェクトを生成するため、`forEach`で待機時間の異なる`Promise`オブジェクトを生成している。これらの`Promise`オブジェクトについて、`Promise.all`を使って一括処理してみようということだ。

```console
"proccess start." 
"resolve :1" 
"resolve :2" 
"resolve :0" 
"resolve." 
"finally." 
```

実行してみると上記のような結果になった。これは、生成した3つの`Promise`オブジェクトについて、すべて解決した場合の結果である。つまり、`Promise.all`は指定された複数の`Promise`オブジェクトについて、**すべて解決であるなら**`Promise.all`の結果も「解決」へ遷移する。

では、複数の`Promise`オブジェクトのうち、拒否状態に遷移したオブジェクトが存在する場合はどうなるのだろうか。

```console
"proccess start." 
"reject :1" 
"reject." 
"finally." 
"resolve :2" 
"resolve :0" 
```

そのときの実行結果が上記である。複数の`Promise`オブジェクトについて、**1つでも拒否状態に遷移した時点で**`Promise.all`も「拒否」状態に遷移している。複数の`Promise`オブジェクトを実行したとき、すべての処理完了を待っているとパフォーマンスが悪い。よって、1つでも拒否状態となったのなら、`Promise.all`としては拒否状態へ遷移することが決定されるので、他の処理の完了を待っていても仕方がない、ということになる。

## reference

1. [TypeScriptでPromiseを書く](https://osamtimizer.hatenablog.com/entry/2018/06/22/121235)
1. [error TS2314: Generic type 'Promise<T>' requires 1 type argument(s)](https://stackoverflow.com/questions/39781618/error-ts2314-generic-type-promiset-requires-1-type-arguments)
1. [Promise.all()で非同期処理を並列に捌く](https://common-engineer.com/javascript/promise-all)
