# DockerコンテナがRestarting状態から変化しなくなってしまった場合ログを見てみる

## detail

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
initdb: error: superuser name "pg_hoge_admin" is disallowed; role names cannot begin with "pg_"
```

この場合、PostgreSQLを構築する際に「pg_hoge_admin」というユーザーを追加しようとしているが、頭に「pg_」が付くスキーマは**システムが利用するため予約されて**おり新規に割り当てることはできない。「その名前は使えないぜ！」とエラーになっていたわけだ。これがエラーの根本原因だが、このエラーはdocker-compose.ymlを編集しない限り解消しない。Dockerはコンテナ起動に失敗してもとりあえずそのままの状態でリスタートするので、コンテナの起動ごとにこのエラーが発生し続ける。よって、コンテナの起動に失敗し続け「Restarting」から抜け出せなくなっていた。今回の場合はユーザー名を変更し、「pg_」のないユーザー名でユーザー追加を行うことで事なきを得た。

コンテナが起動しない場合はリビルドやイメージの再取得をしがちだが、上記のようにコンテナそのものではなく中身が問題であるケースもある。思い込みはキケン。

## reference

1. [Docker: Container keeps on restarting again on again](https://stackoverflow.com/questions/37471929/docker-container-keeps-on-restarting-again-on-again)
2. [docker-compose ps](https://docs.docker.com/compose/reference/ps/)
3. [スキーマ](https://www.postgresql.jp/document/9.1/html/ddl-schemas.html)
