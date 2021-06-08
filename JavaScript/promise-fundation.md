# `Promise`の基本

## detail

`Promise`は、JSで非同期処理を用いる際に利用するオブジェクト。非同期処理が最終的に完了したか、あるいは失敗したかを示す。これだけだと何のこっちゃという感じなので、まず「最終的に完了したか否か」という部分を見てみたい。

```typescript
const getBool = (num = 0.5) => {
  return Math.random() > num;
}

const funcPromise = new Promise(
  (resolve : Function, reject : Function)=>{
    if(getBool()){
      resolve();
    }
    else {
      reject();
    }
  }
)

funcPromise.then(()=>{
  console.log('resolve.');
}).catch(()=>{
  console.log('reject.');
});
```

上記はTypeScriptで書いてあるものの、基本的にはJavaScriptでも同じ書き方。

関数`getBool()`はランダムで真あるいは偽のいずれかを返す。要はこの値によって「処理が成功したか否か」

## reference

1. [Promiseを使う](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Using_promises)
