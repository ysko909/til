# `which`が使えないなら`type`を使えばいいじゃない

## detail

タイトルは出オチなので、気にしてはいけない。

```console
ProductName:	Mac OS X
ProductVersion:	10.15.7
```

上記の環境で実行している。

`which`はコマンドの場所を出力する。

```console
$ which cat
/bin/cat
```

ただ、エイリアスが設定された環境において`which`を実行した場合、コマンドの場所ではなく**エイリアス設定を出力してしまう**。

```console
$ which ls
ls: aliased to ls -G
$ which grep
grep: aliased to nocorrect grep --color=auto
```

まぁ場合によっては有用な情報ではあるのだが、とりあえず現状で知りたいのはアドレスであってエイリアス設定ではない。こういう場合は`type`を利用する。

```console
$ type ls
ls is an alias for ls -G
ls is /bin/ls
$ type grep
grep is an alias for nocorrect grep --color=auto
grep is /usr/bin/grep
```

エイリアス設定とともに、コマンドのアドレスも表示してくれる。

```console
$ which type
type: aliased to type -a
$ type type
type is an alias for type -a
type is a shell builtin
type is /usr/bin/type
```

ちなみに`type`自身は`/usr/bin`配下にいる。

## reference

1. [type](https://ss64.com/osx/type.html)
2. [Why “which” command does not give you the correct path?](https://apple.stackexchange.com/questions/73965/why-which-command-does-not-give-you-the-correct-path)
