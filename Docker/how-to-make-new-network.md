# 新規ネットワークを作成する方法

## detail

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

> ネットワークの作成時は --subnet オプションの指定を強く推奨します。 --subnet を指定しなければ、docker デーモンはネットワークに対してサブネットを自動的に割り当てます。その時、Docker が管理していない基盤上の別サブネットと重複する可能性があります。

オフィシャルのドキュメントには、`--subnet`を指定しろとあるので素直に指定しよう。

```console
docker network create -d bridge --subnet=123.1.3.0/24 --gateway=123.1.3.1 --ip-range=123.1.3.0/24 hoge_nw
```

- `--subnet`：サブネットを指定する。
- `--gateway`：ゲートウェイを指定する。
- `--ip-range`：作成するネットワークについて割り当てるIPアドレスの範囲。

事前に調査しておいて、空いているIPアドレスを指定してやればいい。

```console
docker network inspect hoge_nw
```

`docker network inspect <NETWORK_NAME>`を利用すると、指定したネットワークの詳細を確認できる。

作成したネットワークを削除する場合は`docker network rm <NETWORK_NAME>`を実行する。ただし、削除対象のネットワークにコンテナが接続されている場合はエラーになるため、事前に`docker network disconnect <NETWORK_NAME> <CONTAINER_NAME>`を実行しコンテナをネットワークから切り離しておく。`docker network inspect <NETWORK_NAME>`を実行したときに`Containers`という項目が空（`{}`）になっていたら、そのネットワークに接続しているコンテナは存在しないということ。

## reference

1. [network create](http://docs.docker.jp/engine/reference/commandline/network_create.html)
1. [Docker コンテナ・ネットワークの理解](https://docs.docker.jp/engine/userguide/networking/dockernetworks.html)
2. [network コマンドを使う](https://docs.docker.jp/engine/userguide/networking/work-with-networks.html)
