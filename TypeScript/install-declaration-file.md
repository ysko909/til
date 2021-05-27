# 型定義ファイルがないよと言われたらインストールが必要

## detail

TypeScriptの環境でコードを書き始めると（場合によってだと思うが）エラーを吐くことがある。

```console
Could not find a declaration file for module ‘react’.
```

エラーメッセージはこれ。今回は`create-react-app`を実行した環境において、コードを書き始めた際に上記のエラーが表示された。

このメッセージで調べてみるとtsconfig.jsonに、`“noImplicitAny”: false,`と書けばいいと言っているブログの記事があったりする。このオプションがTrueだと、型定義のない変数を暗黙のany扱いすることを禁止する。つまりanyを使いたいなら明示的にanyを書かなければいけない、という制約を課すオプション。逆に言えばFalseではこの制約が働かない。ということは素のJavaScriptを書いているのと大差ないわけで、TypeScriptを使っている意味がなくなってしまう。

じゃあどうするかというと、型情報を定義した**型定義ファイルをインストールする**必要がある。

「型情報のインストールってなんのこっちゃ」という感じだが、TypeScriptは型を識別する都合上「型定義ファイル」というものを必要とする。JSはもともと動的型付けだから型を明記することはないし、ReactはもともとTypeScriptで記述されておらず、create-react-appしただけでは型情報が提供されない。よって、別途型情報を用意してやる必要がある。

型情報は、メジャーなJSライブラリだとすでに用意されていることが多い。既存の型情報を利用するには@typesを使う。

```console
yarn add -D @types/react
```

上記のように`@types/ライブラリ名`で実行すれば、既存の型定義ファイルがインストールされる。

```console
$ yarn add @types/phaser
error An unexpected error occurred: "https://registry.yarnpkg.com/@types%2fphaser: Not found".
```

型情報が存在しない場合は、上記のように「そんな型定義ファイルはないよ」とエラーになる。既存の型定義ファイルがないような場合は、[オリジナルで作成することも可能](http://typescript.ninja/typescript-in-definitelyland/definition-file.html)。

なお、型情報が必要なのはあくまで開発中だけ。型情報があろうとなかろうと、リリースするパッケージに含める必要はないので-Dオプションをつけて開発用の依存関係としてインストールする。

## reference

1. [Adding TypeScript](https://create-react-app.dev/docs/adding-typescript/)
2. [【TypeScript】【React】Could not find a declaration file for module ‘react’.の対処について](https://qiita.com/waniwaninowani/items/7ea8b4eacab296758c19)
3. [TypeScript で型定義ファイル( d.ts )がないときの対処法](https://qiita.com/Nossa/items/726cc3e67527e896ed1e)
4. [JavaScriptの資産と@types](http://typescript.ninja/typescript-in-definitelyland/at-types.html)
