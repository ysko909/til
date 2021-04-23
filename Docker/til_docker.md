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
initdb: error: superuser name "pg_hoge_admin" is disallowed; role names cannot begin with "pg_"
```

この場合、PostgreSQLを構築する際に「pg_hoge_admin」というユーザーを追加しようとしているが、頭に「pg_」が付くスキーマは**システムが利用するため予約されて**おり新規に割り当てることはできない。「その名前は使えないぜ！」とエラーになっていたわけだ。これがエラーの根本原因だが、このエラーはdocker-compose.ymlを編集しない限り解消しない。Dockerはコンテナ起動に失敗してもとりあえずそのままの状態でリスタートするので、コンテナの起動ごとにこのエラーが発生し続ける。よって、コンテナの起動に失敗し続け「Restarting」から抜け出せなくなっていた。今回の場合はユーザー名を変更し、「pg_」のないユーザー名でユーザー追加を行うことで事なきを得た。

コンテナが起動しない場合はリビルドやイメージの再取得をしがちだが、上記のようにコンテナそのものではなく中身が問題であるケースもある。思い込みはキケン。

### reference

1. [Docker: Container keeps on restarting again on again](https://stackoverflow.com/questions/37471929/docker-container-keeps-on-restarting-again-on-again)
2. [docker-compose ps](https://docs.docker.com/compose/reference/ps/)
3. [スキーマ](https://www.postgresql.jp/document/9.1/html/ddl-schemas.html)

## ブリッジネットワークについて基礎知識

### detail

Dockerは、その仮想環境を構築するのに仮想ネットワークを構築し、そのネットワークに対してビルドしたコンテナを接続する。これにより、ホストからコンテナへの通信以外にコンテナ間での通信も可能としている。Dockerは起動するとデフォルトでネットワークを作成する。

- bridge
- host
- none

上記3つは、これらの名前で必ず作成される。作成したコンテナがネットワーク設定について言及しない場合、そのコンテナはbridgeに接続される。

```console
$ docker network ls
NETWORK ID     NAME                                  DRIVER    SCOPE
aaaaaaaaaaaa   bridge                                bridge    local
bbbbbbbbbbbb   host                                  host      local
cccccccccccc   none                                  null      local
dddddddddddd   work_react                            bridge    local
eeeeeeeeeeee   work_postgresql                       bridge    local
```

Dockerのネットワークを参照するためには、`docker network ls`を実行する。上記の例だと、コンテナの`work_react``work_postgresql`はそれぞれ`bridge`ドライバーで接続されている。なお、NETWORK IDは本来はランダムな英数字が割り当てられる。

コンテナをただ利用するだけなら、ネットワークについて意識する必要はあまりない。ただ、コンテナが複数立ち上がっている場合、同一のネットワークに乗り入れていると無関係なコンテナとも通信できてしまう。また、コンテナをネットワークから切り離す場合、デフォルトブリッジネットワークを利用しているとコンテナの停止が必須になってしまうが、ユーザー定義ネットワークから切り離すなら停止させる必要がない。さらに、ユーザー定義ネットワークは、デフォルトブリッジネットワークに比べて個別に設定を変更できるなど自由度が高い。コンテナをそれぞれネットワークごと分離する必要があるなら、ネットワークの作成は必須になる。

ネットワーク個別の内容を確認するには、`docker network inspect NETWORK_NAME`を利用する。

```console
$ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "08dcfb7425de4076eddd811d3ca9f9bb2ee139fdb26f426bf4db0a5c3aff4385",
        "Created": "2021-02-23T23:22:52.3127953Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
・・・以下、いろいろ情報が出力される。
```

なお、デフォルトのネットワークには注意点がある。

> デフォルトの bridge ネットワークは Docker の古い機能の一部に過ぎないので、本番環境での利用はお勧めできません。 

[オフィシャルのドキュメント](https://matsuand.github.io/docs.docker.jp.onthefly/network/bridge/#use-the-default-bridge-network)にも記述があるくらいなので、開発環境ならともかく本番環境にデフォルトを利用するのは控えよう。

### reference

1. [ブリッジネットワークの利用](https://matsuand.github.io/docs.docker.jp.onthefly/network/bridge/)
2. [参考訳：Docker コンテナ・ネットワークの理解](https://qiita.com/zembutsu/items/1da05cb6a60e1c5acc25)
3. [【Docker】任意のネットワークを作成してコンテナ間通信をする手順](https://genchan.net/it/virtualization/docker/10601/)

## コンテナ内で作成したファイルをホストで操作する場合権限でエラーになるケースがある

### detail

`VOLUME`を使ってホストのフォルダをコンテナにマウントすることで、コンテナ内でホストのファイルを使ったり、コンテナ内で作成したファイルをホストで操作できる。ただし、後者の場合はファイルのowner権限を考慮しておかないと問題が起きる。

```console
sampleusr@foohost:~/foo$ id
uid=1000(sampleusr) gid=9999(testgr) groups=9999(testgr),999(docker)
sampleusr@foohost:~/foo$ mkdir hoge && touch hoge/hoge.txt
sampleusr@foohost:~/foo$ ls -al hoge
total 8
drwxr-xr-x 2 sampleusr testgr 4096  4月 20 16:41 .
drwxr-xr-x 4 sampleusr testgr 4096  4月 20 16:41 ..
-rw-r--r-- 1 sampleusr testgr    0  4月 20 16:41 hoge.txt
sampleusr@foohost:~/foo$ docker run -it -v $(pwd)/hoge:/hoge busybox
/ # ls -al /hoge
total 8
drwxr-xr-x    2 1000     9999          4096 Apr 20 07:41 .
drwxr-xr-x    1 root     root          4096 Apr 20 07:42 ..
-rw-r--r--    1 1000     9999             0 Apr 20 07:41 hoge.txt
/ # touch /hoge/foo.txt
/ # ls -al /hoge
total 8
drwxr-xr-x    2 1000     9999          4096 Apr 20 07:43 .
drwxr-xr-x    1 root     root          4096 Apr 20 07:42 ..
-rw-r--r--    1 root     root             0 Apr 20 07:43 foo.txt
-rw-r--r--    1 1000     9999             0 Apr 20 07:41 hoge.txt
/ # exit
sampleusr@foohost:~/foo$ ls -al hoge
total 8
drwxr-xr-x 2 sampleusr testgr 4096  4月 20 16:43 .
drwxr-xr-x 4 sampleusr testgr 4096  4月 20 16:41 ..
-rw-r--r-- 1 root      root      0  4月 20 16:43 foo.txt
-rw-r--r-- 1 sampleusr testgr    0  4月 20 16:41 hoge.txt
sampleusr@foohost:~/foo$ rm hoge/foo.txt
rm: remove write-protected regular empty file 'hoge/foo.txt'?
```

コンテナ内で作成したファイル`foo.txt`はownerがrootになっているため、そのファイルをホストで削除しようとしても（ホストでは一般ユーザーとしてログインしているので）「プロテクトされとるぞ」とメッセージが出力される。

```console
sampleusr@foohost:~/foo$ mkdir hoge
sampleusr@foohost:~/foo$ docker run -it -v $(pwd)/hoge:/hoge busybox
/ # mkdir -p /hoge/fuga/piyo
/ # exit
sampleusr@foohost:~/foo$ ls -al hoge
total 12
drwxr-xr-x 3 sampleusr testgr 4096  4月 21 13:19 .
drwxr-xr-x 4 sampleusr testgr 4096  4月 21 13:18 ..
drwxr-xr-x 3 root      root   4096  4月 21 13:19 fuga
sampleusr@foohost:~/foo$ touch hoge/fuga/piyo/foo.txt
touch: cannot touch 'hoge/fuga/piyo/foo.txt': Permission denied
```

他のケースとしては、コンテナ内で作成されたフォルダに対しファイルを作成しようとすると権限エラーが起きる。フォルダのownerがrootになっているため、ホスト側でログインしている一般ユーザーの権限では足りないからだ。もちろんフォルダの削除もできない。

この現象は、どうもLinuxのホストでLinuxのコンテナを利用するときに発生するようで、ホストがWindowsかmacOSの場合は発現しなかった。

解決方法はいくつがあるが、コンテナ内で`useradd`して一般ユーザーを作成し作成した一般ユーザーで処理を実行する、というもの。ホスト側のユーザーに関するUIDやGIDを渡してやればいい。あるいはDockerコンテナ起動時に`-u`オプションで起動時のUIDやGIDをコンテナに引き継ぐことができる。ただし、後者の方法は`/etc/passwd`と`/etc/group`をコンテナにボリュームマウントする必要がある。

### reference

1. [dockerでvolumeをマウントしたときのファイルのowner問題](https://qiita.com/yohm/items/047b2e68d008ebb0f001)
2. [Docker コンテナ内で Docker ホストと同じユーザを使う](https://blog.amedama.jp/entry/docker-container-host-same-user)

## 新規ネットワークを作成する方法

### detail

特定のコンテナ間でのみ通信を行いたい場合、Dockerのネットワークを新規作成しコンテナを接続する。こうすることで、コンテナだけを隔離した状態で相互にアクセスが可能となる。

普段Dockerfileなどによりコンテナを作成する場合、デフォルトで用意されているネットワークに自動的に接続される。ネットワークの一覧を参照したい場合は`docker network ls`を実行する。

```console
$ docker network ls
NETWORK ID    NAME      DRIVER   SCOPE
aaaaaaaaaaaa  bridge    bridge   local
bbbbbbbbbbbb  host      host     local
cccccccccccc  none      null     local
```

上記の3つがデフォルトで用意されているネットワークであり、それぞれに利用用途が異なる。

- bridge：基本のネットワークで、単純にコンテナを作成した場合はこのネットワークに接続される。Dockerホストの`docker0`と接続されており、`bridge`を経由することで他のコンテナなどと通信が可能になる。
- host：Dockerホストそのものを利用する。このネットワーク自体に直結して、コンテナを運用するケースってそんなにない気がする。
- none：このネットワークに接続するとループバックの状態、つまりコンテナがネットワーク的に隔離された状態になる。

NETWORK IDはそれぞれが個別の値を持つ。オリジナルのネットワークを作成した場合も同様。

ネットワークを指定しない場合は`bridge`ネットワークに接続されるが、ネットワーク的に他のコンテナと隔離したい場合はオリジナルのネットワークを作成する。

```console
$ docker network create -d bridge hoge_nw
$ docker network ls
NETWORK ID          NAME                         DRIVER              SCOPE
aaaaaaaaaaaa        bridge                       bridge              local
bbbbbbbbbbbb        host                         host                local
cccccccccccc        none                         null                local
dddddddddddd        hoge_nw                      bridge              local
```

`docker network create`で、新しいネットワークを作成できる。`-d`はドライバーの指定で、今回は別ネットワークを作成する目的であるので`bridge`を指定した。

```console
docker network inspect hoge_nw
```

`docker network inspect <NETWORK_NAME>`を利用すると、指定したネットワークの詳細を確認できる。

作成したネットワークを削除する場合は`docker network rm <NETWORK_NAME>`を実行する。ただし、削除対象のネットワークにコンテナが接続されている場合はエラーになるため、事前に`docker network disconnect <NETWORK_NAME> <CONTAINER_NAME>`を実行しコンテナをネットワークから切り離しておく。`docker network inspect <NETWORK_NAME>`を実行したときに`Containers`という項目が空（`{}`）になっていたら、そのネットワークに接続しているコンテナは存在しないということ。

### reference

1. [Docker コンテナ・ネットワークの理解](https://docs.docker.jp/engine/userguide/networking/dockernetworks.html)
2. [network コマンドを使う](https://docs.docker.jp/engine/userguide/networking/work-with-networks.html)
