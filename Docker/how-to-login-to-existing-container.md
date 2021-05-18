# すでに存在しているコンテナに入る

## detail

```console
docker run -it busybox /bin/sh
```

たとえば上記のように実行した場合は、新規で`busybox`のコンテナを立ち上げてからコンテナに入る。つまり、もともと起動しているコンテナではない。では、起動済みで今現在稼働中のコンテナに入るにはどうするか？

```console
docker exec -it CONTAINER_NAME /bin/bash
```

既存のコンテナに入る場合は、`docker exec`を使う。入りたいコンテナを指定すれば入れる。

## reference

1. [docker runとdocker execの違いの解説](https://www.memotansu.jp/docker/852/)
2. [後からdockerコンテナにbashで入る方法【execコマンド】](https://unskilled.site/%E5%BE%8C%E3%81%8B%E3%82%89docker%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%81%ABbash%E3%81%A7%E5%85%A5%E3%82%8B%E6%96%B9%E6%B3%95%E3%80%90exec%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%80%91/)
