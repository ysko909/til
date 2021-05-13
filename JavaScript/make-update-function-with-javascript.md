# JavaScriptでのアップロード機能の実装

## detail

JavaScriptでローカルの画像をアップロードし、canvas上に表示するプログラムを作成。HTMLとJavaScriptなので、PlayCode上にて仮実装。ソースは[gist](https://gist.github.com/ysko909/0f14a71c5604ded658e3a4f8fb837e6d)にアップした。

[参考元URL](https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html)では、画像ファイルであれば受け付けていたが、ここではPNGのみを対象とした。`type.match()`の対象を、`image/png`とするとPNGに限定できる。もともとの仕様では画像がアップロードされた場合のみcanvasの更新を行っていたため、対象外のファイルがアップロードされた場合は、過去にアップロードされたデータや子要素が画面に残り続ける。それを解消するため、PNG以外がアップロードされた場合は一度canvasを削除する仕様とした。また、同様にダウンロード用URLが存在する場合は子要素から削除する。

## reference

1. [File APIとCanvasでローカルの画像をアップロード→加工→ダウンロードする](https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html)
1. [ソースのgist](https://gist.github.com/ysko909/0f14a71c5604ded658e3a4f8fb837e6d)
