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

```console
"proccess start." 
"resolve :1" 
"resolve :2" 
"resolve :0" 
"resolve." 
"finally." 
```

```console
"proccess start." 
"reject :1" 
"reject." 
"finally." 
"resolve :2" 
"resolve :0" 
```

## reference

1. [TypeScriptでPromiseを書く](https://osamtimizer.hatenablog.com/entry/2018/06/22/121235)
1. [error TS2314: Generic type 'Promise<T>' requires 1 type argument(s)](https://stackoverflow.com/questions/39781618/error-ts2314-generic-type-promiset-requires-1-type-arguments)
