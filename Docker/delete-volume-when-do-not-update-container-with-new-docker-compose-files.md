# コンテナやイメージを削除してもDockerfileやdocker-compose.ymlの内容が反映されない場合はvolumeを削除する

## detail

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

## reference

1. [Use volumes](https://docs.docker.com/storage/volumes/)
1. [Dockerのイメージ、コンテナおよびボリュームを削除する方法](https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes-ja)
