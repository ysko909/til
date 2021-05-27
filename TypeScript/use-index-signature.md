# インデックスシグネチャで連想配列を使う

## detail

TSにて連想配列を取り扱う際に出てくるのがインデックスシグネチャ。インデックスシグネチャを用いて、連想配列に含まれるキーや値の型を指定できる。

```typescript
interface Hoge{
    [key:string]:number;
}

const hoge: Hoge ={
    foo:1,
    bar:2,
    baz:3
};

console.log(hoge['foo']);
console.log(hoge.foo); // ピリオドでもOK
hoge.foo = 100;
console.log(hoge['foo']);
console.log(hoge.foo);

// hoge.foo = 'hoge'; これは「値はnumberだからstringは代入できないぜ」エラー

hoge.foa = 9999; // 「foo」を入力しようとしてタイポ
console.log(hoge.foo);
console.log(hoge.foa);

// hoge.foo.foobarbaz = 9999; これは「プロパティなし」エラー
```

`interface`を使って、連想配列のキーと値の型をそれぞれ指定する。ここではキーがstring、値がnumberとした。この設定に一致する場合は、値の設定が可能。

```console
1 
1 
100 
100 
100 
9999 
```

ちなみに、連想配列は**もともとなかったキーと値を新たに設定することができてしまう**。「もともと存在するキー`foo`の値を書き換えようとして、タイポしたキー`foa`に値を設定してしまう」という可能性はまあまあ考えられるので注意が必要。なお、この場合は新しい要素として追加される。そのため、`foo`が新しい値に置き換わっていると思い込んで参照してみると、古い値が返ってくる（新しい値は`foa`側に設定されてしまっている）のでバグの原因にもなりかねない。

## reference

1. [第9回　連想配列の取り扱い方](https://www.atmarkit.co.jp/ait/articles/1501/29/news117_4.html)
2. [Index signature（インデックス型）](https://typescript-jp.gitbook.io/deep-dive/type-system/index-signatures)
3. [TypeScript：インデックスシグネチャが型安全を破壊する例](https://qiita.com/aakasaka/items/0b081c90b1b99b82143c)
