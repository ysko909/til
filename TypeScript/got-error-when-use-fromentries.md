# `Object.fromEntries()`をTypeScriptで使う場合targetがES2019以降じゃないとエラーになる

## detail

```console
let hoge = [10, 11, 12];
let fuga = ['foo', 'bar', 'baz'];

let piyo = [];
hoge.forEach((val, idx)=>{
    piyo.push([val, fuga[idx]]);
});

let foo = Object.fromEntries(piyo);

console.log(foo);
```

上記のようなコードを書いたとき、codepenなどのJS環境では動作するがTS環境だとエラーになる場合がある。

```console
Property 'fromEntries' does not exist on type 'ObjectConstructor'. Do you need to change your target library? Try changing the `lib` compiler option to 'es2019' or later.
```

結論から言うと、tscondig.jsonファイルの`target`という項目が`es2019`以上になっていない場合、エラーが発生する。

> It looks to me that in tsconfig.json, the setting to use is target: "es2019" or greater. This means it won't work in all browsers and typescrcipt does no magic to use this.

これは、`Object.fromEntries()`がes2019において採用された機能であり、それより以前でのTS環境ではコンパイルエラーと扱われるため。JS環境ではそもそも最新版として動作しているが、一度コンパイルが必要なTS環境ではどのバージョンを目的としてコンパイルするかを設定する必要があり、それが`target`で指定されたECMAScriptのバージョンであるわけだ。よって、`target`が`Object.fromEntries()`を実装したES2019以降でなければコンパイルできずにエラーになりまっせ、というメッセージなわけだ。

## reference

1. [Object.fromEntries()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries)
2. [Add ES2019 Object.fromEntries function #30933](https://github.com/microsoft/TypeScript/issues/30933)
