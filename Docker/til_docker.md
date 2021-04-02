# TIL - Docker

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

## コンテナやイメージを削除してもDockerfileやdocker-compose.ymlの内容が反映されない場合はvolumeを削除する

### detail

Dockerfileやdocker-compose.ymlの内容を更新した場合、基本的にはリビルドして内容をコンテナに反映させる。

```console
docker-compose build --no-cache
docker-compose up -d --build --force-recreate
```

`docker-compose`の場合、上記のようなコマンドを発行するとキャッシュを無視してリビルドしたり、ymlの内容に変更がなくてもイメージの再作成から実行してくれる。

ところが、場合によってはこれだけでは反映されない場合がある。

```yml
version: '3'

services:
    postgres:
        image: postgres
        container_name:  postgres_hoge
        restart: always
        environment:
            TZ: "Asia/Tokyo"
            POSTGRES_USER: admin
            POSTGRES_PASSWORD: admin
            POSTGRES_DB: hoge
        ports:
            - 30000:5432
        volumes:
            - postgres:/var/lib/postgresql/data
            - ./postgres/initdb:/docker-entrypoint-initdb.d

    pgadmin:
        image: dpage/pgadmin4
        container_name:  pgadmin_hoge
        restart: always
        ports:
            - 30001:80
        environment:
            PGADMIN_DEFAULT_EMAIL: admin@example.com
            PGADMIN_DEFAULT_PASSWORD: admin
        volumes:
            - pgadmin:/var/lib/pgadmin
        depends_on:
            - postgres

volumes:
    postgres:
    pgadmin:

```

上記は、PostgreSQLとpgAdminのコンテナを立ち上げるdocker-compose.ymlファイル。このファイルのうち、`environment`の部分を変更して、コンテナをリビルドしたのだが**変更が反映されなかった**。

```yml
        environment:
            PGADMIN_DEFAULT_EMAIL: hogehoge@example.com
            PGADMIN_DEFAULT_PASSWORD: hogehoge
```

`PGADMIN_DEFAULT_EMAIL`は、pgAdminにログインするメールアドレスを記述する箇所。`PGADMIN_DEFAULT_PASSWORD`は、その名の通りパスワードを記述する箇所。これらについて、`admin@example.com`を`hogehoge@example.com`に変更するなどして、docker-compopse.ymlを変更した。本来であれば、単純にコンテナをリビルドして新しいメールアドレスとパスワードでログインすればいいと思うのだが、なぜか反映されない。つまり変更前のメールアドレスやパスワードでないとpgAdminにログインできない。

`environment`はただの環境変数で、ここではpgAdminのコンテナを立ち上げる際の初期設定にしか利用していないので、そもそもイメージの再取得が必要ないと思うのだが何故か反映されない。`docker-compose down --rmi all`などでイメージの削除を行ってから、再度`pull`しても駄目。

原因は**volumeにゴミが残っていた**こと。volumeの内容は、コンテナをリビルドしたりイメージを削除するだけでは消えない。「volumeの内容を削除する」コマンドを発行しないかぎり残り続ける。ログインの情報がvolume側に残存していたため、いくらコンテナをリビルドしてもイメージを再取得しても反映されなかったわけだ。

```console
docker volume ls
docker volume rm hogehoge
```

`docker volume ls`で、volumeの一覧が参照できる。このうち削除したいvolumeを`docker volume rm hogehoge`で指定する。これでvolumeが削除できる。

### reference

1. []()
