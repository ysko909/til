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
