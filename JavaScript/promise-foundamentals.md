# `Promise`の基本

## detail

### `Promise`は状態を持つオブジェクト

`Promise`は、JSで非同期処理を用いる際に利用するオブジェクト。非同期処理が最終的に完了したか、あるいは拒否したかという状態と、それぞれの状態に遷移した場合に実行するコールバック関数を持つ。まぁ、これだけだと何のこっちゃという感じなので、まず非同期処理はとりあえず横に置いておいて「最終的に完了したか否か」という部分を見てみたい。

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

`Promise`オブジェクとは前述のとおり**状態を持つオブジェクト**であり、「処理中（Pending）」か「解決（Fulfilled）」か「拒否（Reject）」の3つのうちどれかを持つ。最初の状態は「処理中」であり、何らかの処理が正常に終わったのであれば「解決」に遷移し、異常終了すれば「拒否」に遷移する。この「状態が遷移したとき」を判定して、解決時あるいは拒否時にそれぞれに指定しておいたコールバック関数を実行できる。

上記のコードを見てみると、まず目につくのが`new Promise()`の部分。これは、`Promise`オブジェクトをインスタンス化している。さらに、その`Promise`からチェーンされている`.then`は状態が「解決」に遷移した場合に行う処理を、`.catch`は状態が「拒否」に遷移した場合に行う処理を記述する。そして、`Promise`オブジェクトは2つの引数`resolve`と`reject`を取っている。これらはコールバック関数であり、処理が成功したら`resolve`を、処理が拒否したら`reject`を実行する。つまり、`.then`の部分に`resolve`を、`.catch`の部分に`reject`を記述することになる。ただし、`.then`の部分のみでも構文上は問題ない。これにより、状態が遷移した場合にどのような処理を行うかを記述できるわけだ。

ここで、関数`getBool()`はランダムで真あるいは偽のいずれかを返す。要はこの値によって「処理が成功したか否か」という結果を発生させている、と考えればいい。これにより状態が「処理中」から「解決」あるいは「拒否」のいずれかに遷移するので、真（解決）なら`.then`の部分に記述された処理である`resolve()`というコールバック関数を、偽（拒否）なら`.catch`の部分に記述された処理である`reject()`というコールバック関数をそれぞれ実行する。

このコード自体は、そのまま実行すればランダムで「resolve.」あるいは「reject.」という文字列が表示されるだけだ。

### 非同期処理

次に、非同期処理を考える。「非同期処理」とはコードに書かれた順番と、実際に実行した場合の順番が必ずしも一致しないことを表す。たとえば、何らかの処理がピンポイントに遅い場合が考えられる。

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

最初のコードを少し変えてみた。`setTimeout()`で任意の処理を一定の時間が経過しないと実行しないよう、タイマーを仕込んでいる。

さて、これを実行したときコンソールにはどのように出力されるだろうか。

```console
"proccess start." 
"reject." （あるいはresolve.）
"proccess end." 
```

コードの順番で実行されるのであれば、上記のように出力される。だが、実際のところどうなるかと言うと、この通りには**実行されない**のだ、これが。

```console
"proccess start." 
"proccess end." 
"reject." 
```

実際には上記のように出力される。

「"proccess end." 」より前に「"reject." 」が表示されて欲しいのだが、そうならなかった。これが、コードに書いてある順番と実際の挙動が異なる**非同期処理**の特徴だ。`setTimeout()`はコードに記述された処理なので、あくまでも「コードに準拠した処理」であることは間違いない。ただ、出力結果がコードに書かれている順番と一致しないのだ。少なくとも今回は、`setTimeout()`の実行処理はしたので次の処理に即向かうのではなく、`setTimeout()`の実行が「ちゃんと終わってから」次の処理を行って欲しい、という気持ちでコードを書いている。つまり、このコードの記述通りの順番に処理させたい（このことは、「同期処理」という）。よって、この処理をなんとかしてうまいこと動作させたいときに登場するのが、さっきの`Promise`オブジェクト、というわけだ。

ここで、最初のコードを書き直してみる。

```typescript
const getBool = (num = 0.5) => {
  return Math.random() > num;
}

const funcPromise = new Promise(
  (resolve : Function, reject : Function)=>{
    setTimeout(()=>{
      if(getBool()){
        resolve();
      }
      else {
        reject();
      }
    }, 3000);
  }
)

console.log('proccess start.');

funcPromise.then(()=>{
  console.log('resolve.');
}).catch(()=>{
  console.log('reject.');
}).finally(()=>{
  console.log('proccess end.');
});

```

上記のコードは、es2018の環境でないと動作しない点に注意が必要（`finally`がex2018より前だと存在しないため）。

ここでは、関数`funcPromise()`に`setTimeout()`を記述している。これにより、「ちょっと実行に時間のかかる処理」を再現している。このコードで狙った挙動をするだろうか。

```console
"proccess start." 
"reject." 
"proccess end." 
```

というわけで、コードに記述した通りの順番で実行された。

さっきまで問題となっていたのは、「proccess end.」という文字列が「resolve.（あるいはreject.）」より先に表示されてしまう、という点だった。であれば、本来表示されるべき順番として制御できればいい、ということになる。そこで出てくるのが`.finally`だ。`.then`や`.catch`は状態が「解決」あるいは「失敗」になった時点でいずれかが実行されるが、`.finally`はこれら2つのどちらかが実行されたら最後に強制的に実行される。よって、この`.finally`部分に「proccess end.」を出力するコードを記述すれば、狙った順番で出力されるようになる。

ただし、これはこれで注意が必要。

```typescript
const getBool = (num = 0.5) => {
  return Math.random() > num;
}

const funcPromise = new Promise(
  (resolve : Function, reject : Function)=>{
    setTimeout(()=>{
      if(getBool()){
        resolve();
      }
      else {
        reject();
      }
    }, 3000);
  }
)

console.log('proccess start.');

funcPromise.then(()=>{
  console.log('then側の処理'); // 追加
  setTimeout(()=>console.log('resolve.'), 3000); // 追加
}).catch(()=>{
  console.log('reject.');
}).finally(()=>{
  console.log('proccess end.');
});

```

上記のように、`.then`の部分を書き換えてみる。ここでも`setTimeout()`を使ってタイマーを仕込んでいる。さて、上記のコードは実行するとどのように出力されるだろうか。まぁなんとなく予想はつくと思うが。

```console
"proccess start." 
"then側の処理" 
"proccess end." 
"resolve." 
```

「resolve.」の出力が一番最後になってしまっている。

つまり、`.then`の部分が実行された後で`.finally`の部分のコードが実行されるわけだが、`.then`の部分に追加で記述したタイマーの状態を管理する`Promise`は**記述されていない**。よって、`.then`の部分のコードは今まで通り「`setTimeout()`は実行したよ！**その結果は知らないけど**な！じゃあ次の処理行くねー！」となってしまうわけだ。許さん。よって、ここでも制御が必要ということであれば、別途`Promise`オブジェクトを生成する必要がある。

### まとめ

今まで見てきた通り、「いつ終わるかわからない」非同期処理の終了（解決）あるいはエラー（失敗）をトリガーに処理が実行できること、さらに処理の完了を待って次の処理に移れることが`Promise`の最大のメリットです。実は戻り値が`Promise`でした、なんていうメソッドもたくさん存在しているので覚えておいて損はありません。

## reference

1. [Promiseを使う](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Using_promises)
2. [PromiseによるJavaScript非同期処理レシピ集](https://sbfl.net/blog/2019/11/04/promise-cookbook/)
3. [JavaScriptのPromiseって何？を解決したい](https://reffect.co.jp/html/promise-is-what)
4. [【JavaScript】初心者にもわかるPromiseの使い方](https://techplay.jp/column/581)
5. [Property 'finally' does not exist on type 'Promise<void>'](https://stackoverflow.com/questions/52079031/property-finally-does-not-exist-on-type-promisevoid)
