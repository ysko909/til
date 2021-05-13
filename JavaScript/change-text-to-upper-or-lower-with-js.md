## 英字の文字列を大文字または小文字に変換する

### detail

英字の文字列は`toUpperCase()`を使って大文字に、`toLowerCase()`を使って小文字に変換できる。

```javascript
const $=arg=>{
  console.log(arg);
};

let hoge = 'hOgE';

$(hoge.toUpperCase());
$(hoge.toLowerCase());

$(hoge.slice(0, 1).toUpperCase() + hoge.slice(1).toLowerCase());
```

`toUpperCase()`と`toLowerCase()`、さらに`slice()`を組み合わせることで「頭文字だけを大文字に、他を小文字に変換する」なんてこともできる。

### reference

1. [大文字、小文字に変換する方法](https://lab.syncer.jp/Web/JavaScript/Snippet/45/)
