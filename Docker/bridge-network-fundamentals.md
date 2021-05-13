# ブリッジネットワークについて基礎知識

## detail

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

## reference

1. [ブリッジネットワークの利用](https://matsuand.github.io/docs.docker.jp.onthefly/network/bridge/)
2. [参考訳：Docker コンテナ・ネットワークの理解](https://qiita.com/zembutsu/items/1da05cb6a60e1c5acc25)
3. [【Docker】任意のネットワークを作成してコンテナ間通信をする手順](https://genchan.net/it/virtualization/docker/10601/)
