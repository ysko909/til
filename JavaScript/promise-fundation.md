# `Promise`の基本

## detail

## `Promise`は状態を持つオブジェクト

`Promise`は、JSで非同期処理を用いる際に利用するオブジェクト。非同期処理が最終的に完了したか、あるいは失敗したかを示す。まぁ、これだけだと何のこっちゃという感じなので、まず非同期処理はとりあえず横に置いておいて「最終的に完了したか否か」という部分を見てみたい。

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

`Promise`オブジェクとは**状態を持つオブジェクト**であり、「処理中（Pending）」か「解決（Fulfilled）」か「失敗（Reject）」の3つのうちどれかを持つ。最初の状態は「処理中」であり、何らかの処理が正常に終わったのであれば「解決」に遷移し、異常終了すれば「失敗」に遷移する。この「状態が遷移したとき」を判定して、解決時あるいは失敗時にそれぞれに指定しておいたコールバック関数を実行できる。

上記のコードを見てみると、まず目につくのが`new Promise()`の部分。これは、`Promise`オブジェクトをインスタンス化している。そして、`Promise`オブジェクトは2つの引数`resolve`と`reject`を取っている。これらはコールバック関数であり、処理が成功したら`resolve`を、処理が失敗したら`reject`を実行する。ただし、場合によっては`resolve`のみでも構文上は問題ない。ここで、関数`getBool()`はランダムで真あるいは偽のいずれかを返す。要はこの値によって「処理が成功したか否か」という結果を発生させている、と考えればいい。上記のコードは、真（解決）なら`resolve()`というコールバック関数を、偽（失敗）なら`reject()`というコールバック関数をそれぞれ実行してね、と記述している。

これ自体はそのまま実行すれば、ランダムで「resolve.」あるいは「reject.」という文字列が表示される。

### 非同期処理

次に、非同期処理を考える。

```typescript
const getBool = (num = 0.5) => {
  return Math.random() > num;
}

console.log('proccess start.');

setTimeout(()=>{
  if(getBool()){
    console.log('resolve.');
  }
  else {
    console.log('reject.');
  }
}, 3000);

console.log('proccess end.');
```

最初のコードを少し変えてみた。さて、これを実行したときコンソールにはどのように出力されるだろうか。

```console
"proccess start." 
"reject." （あるいはresolve.）
"proccess end." 
```

上記のように出力され・・・**ない**のだ、これが。

```console
"proccess start." 
"proccess end." 
"reject." 
```

実際には上記のように出力される。「"proccess end." 」より前に「"reject." 」が表示されて欲しいのだが、そうならなかった。これが、コードに書いてある順番と実際の挙動が異なる**非同期処理**の特徴だ。ただ、少なくとも今回は「"proccess end." 」より前に「"reject." 」が表示されて欲しい。よって、このコードの記述通りに処理させたい（このことは、「同期処理」という）。よって、この処理をなんとかしてうまいこと動作させたい。そこで登場するのがさっきの`Promise`オブジェクト、というわけだ。

ここで、最初のコードを書き直してみる。

```typescript
const getBool = (num = 0.5) => {
  return Math.random() > num;
}

const funcPromise = new Promise(
  (resolve : Function, reject : Function)=>{
    setTimeout(()=>{ // 追加
      if(getBool()){
        resolve();
      }
      else {
        reject();
      }
    }, 3000);
  }
)

console.log('proccess start.'); // 追加

funcPromise.then(()=>{
  console.log('resolve.');
}).catch(()=>{
  console.log('reject.');
});

console.log('proccess end.'); // 追加

```

`setTimeout()`でタイマーを設定し、コンソールに出力する2行を追加した。


## reference

1. [Promiseを使う](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Using_promises)
