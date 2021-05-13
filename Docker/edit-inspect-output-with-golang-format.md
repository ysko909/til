# `docker network inspect`でネットワーク名とサブネットだけを抽出するにはGolangのフォーマットで指定する

## detail

`docker network inspect`でネットワークの詳細を表示できるが、ただ出力するだけだといろいろと雑多な情報も十把一絡げに表示される。そこで出力する項目を絞りたいのだが、その場合Golangのフォーマットで指定する必要がある。この[フォーマット](https://golang.org/pkg/text/template/)自体はGolangらしくわかりやすい仕様なので、そこまで困らないと思う。

試しにあるDocker環境のネットワークすべてについて、ネットワーク名とサブネットマスクだけを出力してみる。

```console
$ docker network ls | tail -n +2 | awk '{print $2}' | xargs docker network inspect --format='{{.Name}} : {{range .IPAM.Config}}{{.Subnet}}{{end}}'
bridge : 194.1.2.1/24
bridge_new : 194.1.1.0/24
hogefugapiyo: 172.22.0.0/16
foobarbaz : 172.23.0.0/16
host :
none :
hameggsspam : 192.168.160.0/29
```

単純に`docker network inspect <NETWORK_NAME>`と実行すると、既存のネットワーク名を入力する必要がある。そこで、ネットワーク名は`docker network ls`の2列目を`awk`で取得して、取得結果を`xargs`を使って`docker network inspect`の引数にしてやる。すると、ワンライナーでネットワーク名の取得から各ネットワークの詳細を出力できる。

あとはGolang仕様のフォーマットで出力形式を指定するだけでいい。今回は「ネットワーク名:サブネット」の形式で出力したかったので、`'{{.Name}} : {{range .IPAM.Config}}{{.Subnet}}{{end}}'`と記述した。`{{}}`で囲ったところが1つの出力となり、階層構造になっている出力内容についてピリオドで連結して指定する。

```console
"IPAM": {
    "Driver": "default",
    "Options": {},
    "Config": [
        {
            "Subnet": "192.168.160.0/29",
            "Gateway": "192.168.160.1"
        }
    ]
},
```

注意が必要なのはたまに配列がいることで、サブネットが格納されている箇所は階層構造で言うとIPAM＞Config＞Subnetになるのだが、実際はIPAM＞Config＞[{Subnet, Gateway}]とサブネット部分が配列になっている。そのため、フォーマットを指定する際に`{{.IPAM.Config.Subnet}}`と記述してしまうと、「そんな項目ねえよ」とエラーになってしまう。そこで`range`を使って「.IPAM.Configの中に含まれる要素を参照して、その中から.Subnetという名前の要素を出力する」という方法を取っている。Golangはさっぱりなので他にもっといいやり方があるかもしれないが、とりあえず現状はこれで出力が可能。

## reference

1. [inspect](https://docs.docker.jp/engine/reference/commandline/inspect.html)
2. [Package template](https://golang.org/pkg/text/template/)
