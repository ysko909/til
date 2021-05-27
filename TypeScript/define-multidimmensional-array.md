## 多次元配列の宣言方法

### detail

```typescript
let hoge:(number | string | symbol)[][];
let fuga:(number | string | symbol)[][][];

hoge = [[1, 2, 'foo'], [3, 'bar', 4, 5], ['baz']];
fuga = [[[1, 2], ['foo'],], [['bar', 3], ['baz', 4, 5, 6], [7]], [[8]], [['ham', 'eggs'], ['spam', 9, 10]]];
```

配列の型宣言は`型[]`だが、複数の型を受け付けてしかも多次元配列の場合は、上記のような指定を行えばいい。2次元配列なら`[][]`でOK。

### reference

1. [「型指定の方法」と「基本型の種類」](https://www.wakuwakubank.com/posts/499-typescript-type/)
