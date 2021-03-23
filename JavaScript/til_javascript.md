# TIL - JavaScript

JSに関することはここに書く。

## JavaScriptでのアップロード機能の実装

### detail

- JavaScriptでローカルの画像をアップロードし、canvas上に表示するプログラムを作成。HTMLとJavaScriptなので、PlayCode上にて仮実装。
- [参考元URL](https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html)では、画像ファイルであれば受け付けていたが、ここではPNGのみを対象とした。`type.match()`の対象を、`image/png`とするとPNGに限定できる。
- もともとの仕様では画像がアップロードされた場合のみcanvasの更新を行っていたため、対象外のファイルがアップロードされた場合は、過去にアップロードされたデータや子要素が画面に残り続ける。それを解消するため、PNG以外がアップロードされた場合は一度canvasを削除する仕様とした。また、同様にダウンロード用URLが存在する場合は子要素から削除する。
- PlayCodeは特定のボタンを押すと、ソースのフォーマットを行ってくれる。その際、アロー関数に引数がある場合は**かっこを外す**（引数がない場合はかっこを省略できないので、そのまま残る）。「挙動は一緒なんだから、文字は少ない方がいいでしょ」という判断なのかもしれないが、個人的にはまだアロー関数に慣れてないので、あんまり省略されると混乱する。
- アロー関数ではかっこだけでなく、1行で記述する場合は波かっこまで省略可能なので、ぱっと見の可読性に難があって割と混乱しがち。
- どっちがいいかはともかくとして、「どっちでも読める」程度には慣れたい。

### reference

1. [File APIとCanvasでローカルの画像をアップロード→加工→ダウンロードする](https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html)
1. [ソースのgist](https://gist.github.com/ysko909/0f14a71c5604ded658e3a4f8fb837e6d)

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
