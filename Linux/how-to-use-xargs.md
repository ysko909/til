# コマンドの実行結果を引数として引き継ぐときは`xargs`を使う

## detail

あるコマンドの実行結果を次に実行するコマンドに引き継ぎたい場合、よく利用するのはパイプ（`|`）で連結するパターン。ただし、これは「実行結果を次のコマンドの**入力として**引き継ぐ」方法で、実行結果を次のコマンドの**引数として**引き継ぐわけではない。よって、`ls`の結果をパイプし`grep`への入力とすることで出力を絞り込むことはできるが、`ls`で出力されたファイルに対し`awk`を順次実行するということはできない。

引数として引き継ぐには、パイプとともに`xargs`コマンドを利用する。

```console
$ cat hoge.txt
foo
bar
baz
$ pwd
/root
$ ls
hoge.txt
$ awk '{print $1}' hoge.txt | xargs mkdir
$ ls
bar       baz       foo       hoge.txt
```

使い方は、パイプで繋ぎ前のコマンドの実行結果を引き継ぎ、`xargs 実行したいコマンドとオプション`という形で記述する。

上記では`awk`の結果を`mkdir`に引き継いで、ファイル`hoge.txt`に記述されている内容でフォルダを作成している。

```console
$ ls
bar       baz       foo       fuga.txt  hoge.txt  piyo.txt
$ ls | grep ".*.txt"
fuga.txt
hoge.txt
piyo.txt
$ ls | grep ".*.txt" | xargs cat
11 12
13
14 15 16 17
18 19 20
1 2
3 4 5
6 7 8 9
10
100
200 300
400 500 600
700 800 900 1000
$ ls | grep ".*.txt" | xargs awk '{print FILENAME ":" $1 " " $2 " " $3 " ans: " $1 + $2 + $3;}'
fuga.txt:11 12  ans: 23
fuga.txt:13   ans: 13
fuga.txt:14 15 16 ans: 45
fuga.txt:18 19 20 ans: 57
hoge.txt:1 2  ans: 3
hoge.txt:3 4 5 ans: 12
hoge.txt:6 7 8 ans: 21
hoge.txt:10   ans: 10
piyo.txt:100   ans: 100
piyo.txt:200 300  ans: 500
piyo.txt:400 500 600 ans: 1500
piyo.txt:700 800 900 ans: 2400
```

テキストファイルが持つ数値を加算しファイルごとに結果を出力する、なんて処理もパイプと`xargs`を使うことで実現できる。`awk`の`FILENAME`という文字列は、処理対象のファイル名を出力してくれる。

## reference

1. [xargsコマンド](https://hydrocul.github.io/wiki/commands/xargs.html)
2. [AWKの基本コマンドと使い方を徹底解説！テキスト加工やパターン・文字列処理の方法は？スクリプトとオプションも確認しよう](https://agency-star.co.jp/column/awk/)
