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

## Node.jsの環境をDockerで用意する

### detail

Node.jsの環境をDockerコンテナで用意する。

```console
docker-compose.yml
  └── src/
```

- フォルダ構成は上記のような感じ。Node.jsのオフィシャルコンテナを利用するので、docker-compose.ymlのみ用意する。

```dockerfile
version: '3'

services:
  nodejs:
    image: node:14-alpine

    container_name: work_nodejs

    tty: true

    volumes:
      - ./src:/app

    working_dir: /app
    
    ports:
      - "3000:3000"

    command: yarn start
```

- Node.jsは14のAlpine Linuxを使う。
- あとは`docker-compose run`するかvscodeで「Reopen in container」すればいい。

### reference

1. [DockerでNode.jsアプリケーションを開発する (1) Express.jsをコンテナ内で動かす](https://ishida-it.com/blog/post/2019-11-21-docker-nodejs/)
