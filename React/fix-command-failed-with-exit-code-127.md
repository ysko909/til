# `command failed with exit code 127.`と言われたら`yarn install`する

## detail

React製のプロジェクトをGitHubからダウンロードしてきて、実行するのに`yarn start`したところでエラーが表示された。

```console
command failed with exit code 127.
```

コード127でエラーが表示される場合は、`yarn install`を行う。このコードが出現する理由は、モジュールをインストールしてないせいだからだ。言われてみれば、Node.js関連の構成ファイルはあっても、`node_modules`フォルダとかは`.gitignore`で無視されているため、GitHub上には上がってないんだった。

## reference

1. [command failed with exit code 127.](https://github.com/reactstrap/reactstrap/issues/711)
