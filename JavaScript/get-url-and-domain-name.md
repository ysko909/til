# URLやドメインを取得する

## detail

JavaScriptが実行されているURLやドメインは、以下のコードを取得できる。

```javascript
console.log(document.URL);
console.log(document.domain);
```

これだけでいい。

```console
https://playcode.io/new/
playcode.io
```

もちろん、返ってくる値はこのコードを実行しているURLによって異なる。

なお、ブラウザの開発者ツールを開いて、コンソールにこのコードを入力して実行すると、今アクセスしているwebページのURLなどが取得できる。

ちなみに、`Document.URL`は読み取り専用だが、`Document.domain`は読み書きができる。まぁ書くことはそうそうないだろうが。

## reference

1. [Document.URL](https://developer.mozilla.org/ja/docs/Web/API/Document/URL)
2. [Document.domain](https://developer.mozilla.org/ja/docs/Web/API/Document/domain)
