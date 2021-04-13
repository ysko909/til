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
  2. `npm install`し、必要なパッケージをすべてインストールする
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

`docker-compose`の場合、上記のようなコマンドを発行するとキャッシュを無視（`--no-cache`）してリビルドしたり、ymlの内容に変更がなくてもイメージの強制再作成（`--force-recreate`）から実行してくれる。

ところが、場合によっては**これだけでは反映されないことがある**。

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

`PGADMIN_DEFAULT_EMAIL`は、pgAdminにログインするメールアドレスを記述する箇所。`PGADMIN_DEFAULT_PASSWORD`は、その名の通りパスワードを記述する箇所。これらについて、`admin@example.com`を`hogehoge@example.com`に変更するなどして、docker-compopse.ymlを更新した。本来であれば、単純にコンテナをリビルドして新しいメールアドレスとパスワードでログインすればいいと思うのだが、なぜか反映されない。つまり変更前のメールアドレスやパスワードでないとpgAdminにログインできない。

`environment`はコンテナ内で利用する単なる環境変数で、ここではpgAdminのコンテナを立ち上げる際の初期設定にしか利用していないはず。`docker-compose down --rmi all`などでイメージの削除を行ってから、再度`pull`しても駄目。そもそもイメージの再取得は必要なく、`docker-compose up --build -d`とかでいいと思うのだがどちらにせよ何故か反映されない。

原因は**volumeにゴミが残っていた**こと。volumeの内容は、コンテナをリビルドしたりイメージを削除するだけでは消えない。「volumeの内容を削除する」コマンドを発行しないかぎり残り続ける。つまり、ログインに必要なアカウント情報がvolume側に残存していたため、いくらコンテナをリビルドして新しい環境変数にしてもイメージを再取得しても、それらは残存し続けるvolumeには関係ないため反映されなかったわけだ。

```console
docker volume ls
docker volume rm hogehoge
```

`docker volume ls`で、volumeの一覧が参照できる。このうち削除したいvolumeを`docker volume rm hogehoge`で指定する。これでvolumeが削除できる。

一応注意点は、volumeを削除することはそのコンテナで利用していたデータボリュームを削除することになるので、重要なデータは事前に別コンテナなどに退避させる必要がある。コンテナを作ったばっかりならいいかもしれないが、ある程度運用しているようなコンテナについてvolumeをいじる場合は気をつけたい。

### reference

1. [Use volumes](https://docs.docker.com/storage/volumes/)
1. [Dockerのイメージ、コンテナおよびボリュームを削除する方法](https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes-ja)

## DockerコンテナがRestarting状態から変化しなくなってしまった場合ログを見てみる

### detail

docker-compose.ymlを編集して`docker-compose up -d`したところ、一度は起動したコンテナがRestartingの状態に変化し、その後ずっとRestartingから変化しなくなってしまった。つまり、起動して何らかの原因でコンテナが落ちて再起動して、また落ちて再起動して・・・を繰り返していることになる。

```console
docker-compose ps
```

`ps`コマンドでコンテナの状態（status）を見てみると、起動直後は「Up」なのだが少ししてから再度`ps`で見てみると「Restarting」に変化していて、以降はずっと「Restarting」から変化しない。

結論から言うと、コンテナが悪いわけではなく**コンテナ起動に関してエラーが起こっている**可能性が高い。よって、その原因を解消しないかぎりコンテナは正常に起動しない。

```yml
version: '3'

services:
    postgres:
        image: postgres
        container_name:  postgres_hoge
        restart: always
        environment:
            TZ: "Asia/Tokyo"
            POSTGRES_USER: pg_hoge_admin
            POSTGRES_PASSWORD: pg_hoge_admin
            POSTGRES_DB: pg_hoge
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

上記のようなdocker-compose.ymlを作成して`docker-compose up -d`したところ、PostgreSQL側のコンテナだけRestartingから抜け出せなくなってしまった。コンテナのリビルドやイメージの再取得をしてもダメ。

こういう場合は、正常起動しないコンテナのログを参照してエラーが起きていないかチェックした方がいい。

```console
docker logs --tail 50 --follow --timestamps postgres_hoge
```

上記のコマンドを実行すると、指定したコンテナ（上記だと`postgres_hoge`コンテナを指す）のログの最新50件分を表示する。大抵の場合、このログを見てみることで何かしらのエラーログを見つけられるはず。

```console
initdb: error: superuser name "pg_sonar_admin" is disallowed; role names cannot begin with "pg_"
```

この場合、PostgreSQLを構築する際に「pg_hoge_admin」というユーザーを追加しようとしているが、頭に「pg_」が付くスキーマは**システムが利用するため予約されて**おり新規に割り当てることはできない。「その名前は使えないぜ！」とエラーになっていたわけだ。これがエラーの根本原因だが、このエラーはdocker-compose.ymlを編集しない限り解消しない。Dockerはコンテナ起動に失敗してもとりあえずそのままの状態でリスタートするので、コンテナの起動ごとにこのエラーが発生し続ける。よって、コンテナの起動に失敗し続け「Restarting」から抜け出せなくなっていた。今回の場合はユーザー名を変更し、「pg_」のないユーザー名でユーザー追加を行うことで事なきを得た。

コンテナが起動しない場合はリビルドやイメージの再取得をしがちだが、上記のようにコンテナそのものではなく中身が問題であるケースもある。思い込みはキケン。

### reference

1. [Docker: Container keeps on restarting again on again](https://stackoverflow.com/questions/37471929/docker-container-keeps-on-restarting-again-on-again)
2. [docker-compose ps](https://docs.docker.com/compose/reference/ps/)
3. [スキーマ](https://www.postgresql.jp/document/9.1/html/ddl-schemas.html)
