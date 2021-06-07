# 処理時間を計測する

## detail

ある任意の処理について、その処理時間を計測したいとする。

```javascript
const start = Date.now();
hoge(); // 計測したい処理
const end = Date.now();

console.log(end - start);
```

上記のコードでも一応計測はできる。ただ、結構大きな問題があって、それが`Date.now()`が**ミリ秒までしか計測できない**こと。スマートフォンに限らず、最近デバイスはそもそもハード設計からして高速であることと、その上で動作している[V8](https://v8.dev/)を始めたとしたJavaScriptエンジンも十分に高速であることから、ミリ秒程度の精度では細かいところまで見切れないという印象がある。`Date.now()`が精緻な値を取得できないのであれば、別な方法を使えばいい。

```javascript
let start = performance.now();
hoge();
let end = performance.now();

console.log(end - start);
```

その方法とは、`performance.now()`といういかにもな名前のメソッド。こちらは1マイクロ秒の精度を持つ。

これを使ってたとえばソートに掛かる処理時間を計測してみる。

```typescript
const bubbleSort = (numbers: number[]) => {
  let temp = numbers;
  for (let i=0; i < temp.length; i++) {
    for (let j = temp.length-1; j > i; j--) {
      if (temp[j] < temp[j-1]) {
        [temp[j], temp[j-1]] = [temp[j-1], temp[j]];
      }
    }
  }
  return temp;

}

const quickSort = (numbers: number[]) => {
  const recurse = function(a: number[], i:number, j:number) {
      let k, p = pivot(a, i, j);
      if (p !== -1) {
        k = partition(a, i, j, a[p]);
        
        recurse(a, i, k - 1);
        recurse(a, k, j);
      }
      return a;
    },
    pivot = function(a: number[], i:number, j:number) {
      let k = i + 1;
      while (k <= j && a[i] === a[k]) {
        k++;
      }
      if (k > j) {
        return -1;
      }
      if (a[i] >= a[k]) {
        return i;
      }
      return k;
    },
    partition = function(a:number[], i:number, j:number, p:number) {
      let l = i, r = j;
      while (l <= r) {
        while (l <= j && a[l] < p) {
          l++;
        }
        while (r >= i && a[r] >= p) {
          r--;
        }
        if (l > r) {
          break;
        }
        
        [a[l++], a[r--]] = [a[r--], a[l++]];
      }
      
      return l;
    };

  return recurse(numbers, 0, numbers.length - 1);
}

const getRndIntNumbers = (num = 10000) => {
  let numbers = [];
  for (let i=0; i<num; i++){
    numbers.push(Math.floor(Math.random() * 10));
  }

  return numbers;
}

const numbers = getRndIntNumbers();

let start = performance.now();
let result = bubbleSort(numbers);
let end = performance.now();

console.log('bubbleSort:')
console.log(end - start);

start = performance.now();
result = quickSort(numbers);
end = performance.now();

console.log('quickSort:')
console.log(end - start);

```

上のコードはTypeScriptだが、JavaScriptでも書き方は別に変わらないと思う。実行してみると、生成される配列によって処理時間に差はあるものの、総じてクイックソートが早い。まぁ当たり前だね。

## reference

1. [performance.now()](https://developer.mozilla.org/ja/docs/Web/API/Performance/now)
2. [JavaScriptで任意の処理にかかる時間を計測する](https://sbfl.net/blog/2017/12/01/javascript-measure-time/)
3. [How to swap two array elements in JavaScript](https://flaviocopes.com/javascript-swap-array-elements/)
4. [JavaScriptでさまざまなソートアルゴリズム](https://110chang.com/knowledge/javascript-sort-argorythm/)
