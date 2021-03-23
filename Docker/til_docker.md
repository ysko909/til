# til - Docker

Dockerに関することはここに書く。

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
- たとえば[Express.js](https://expressjs.com/)の環境を用意したい場合、以下の手順で簡単に構築できる。
  1. 作成したコンテナ内で`npx express-generator`する。
  2. `npm install`し、必要なパッケージをすべてインストールする。
  3. `npm start`すればExpress.jsが実行される。ブラウザから`localhost:3000`にアクセスして、Express.jsのテストページが表示されれば構築完了。

### reference

1. [DockerでNode.jsアプリケーションを開発する (1) Express.jsをコンテナ内で動かす](https://ishida-it.com/blog/post/2019-11-21-docker-nodejs/)
