# `find`を使ってファイルを検索する

## detail

Linuxでファイルを検索したい場合は、`find`コマンドを使う。

```console
find -name "yarn.lock"
```

上記のように実行すると、カレントディレクトリ配下で`yarn.lock`という名前のファイルを検索して一覧表示する。

```console
$ find -name "logo*.*"
./workapp/node_modules/detect-port-alt/logo.png
./workapp/public/logo192.png
./workapp/public/logo512.png
$ find -name "logo*.*" -not -name "logo192.png"
./workapp/node_modules/detect-port-alt/logo.png
./workapp/public/logo512.png
```

特定のものだけ除外したい場合は`-not`を付与する。すると、`-not`で指定されたファイルあるいは文字列に合致するものが除外される。

```console
find -type d -name "regexp"
```

`-type`を付与すると、検索対象の属性を指定できる。

- `f`：ファイル
- `d`：ディレクトリ
- `l`：シンボリックリンク

他にもあるが、大体利用するところはこんな感じかな。

## reference

1. [Linuxでファイルを検索する：findとlocateの使い方](https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-linux-ja)
