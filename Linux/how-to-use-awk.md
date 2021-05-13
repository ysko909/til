# `awk`を使う

## detail

ファイルやコマンドの出力結果といった文字列を編集するのに有効な`awk`について、簡単な使い方を書く。`awk`自体とても奥深いコマンドなのだが、ここでは軽くワンライナーでざっくり触れる程度の紹介とする。

```console
/home/hoge # ls -al
total 12
drwxr-xr-x    2 root     root          4096 Apr 23 02:53 .
drwxr-xr-x    1 nobody   nobody        4096 Apr 23 02:52 ..
-rw-r--r--    1 root     root            61 Apr 23 02:53 hoge.txt
/home/hoge # ls -al | awk '{print $1}'
total
drwxr-xr-x
drwxr-xr-x
-rw-r--r--
/home/hoge # ls -al | awk '{print $2}'
12
2
1
1
/home/hoge # ls -al | awk '{print $3}'

root
nobody
root
/home/hoge # ls -al | awk '{print $3 $5}'

root4096
nobody4096
root61
/home/hoge # ls -al | tail -n +2 | awk '{print $3 ":" $5}'
root:4096
nobody:4096
root:61
```

コマンド結果を`awk`で整形したければ、パイプで繋げばいい。`$1`の指定は1行目を意味していて、この場合スペースがある列で構成されていると解釈している。`{print $1}`の形式で書くと、「各行の1列目を出力しろ」という意味になる。

```console
/home/hoge # cat hoge.txt
hoge fuga piyo
foo bar baz
ham eggs spam python
/home/hoge # awk '{print}' hoge.txt
hoge fuga piyo
foo bar baz
ham eggs spam python
/home/hoge # awk '{print $1 "///" $3}' hoge.txt
hoge///piyo
foo///baz
ham///spam
/home/hoge # awk 'NR==2{printf "%s-%s-%s\n", $2, $1, $3}' hoge.txt
bar-foo-baz
/home/hoge # awk 'NR==2;{printf "%s-%s-%s\n", $2, $1, $3}' hoge.txt
fuga-hoge-piyo
foo bar baz
bar-foo-baz
eggs-ham-spam
/home/hoge # awk '/g/{printf "%s-%s-%s\n", $2, $1, $3}' hoge.txt
fuga-hoge-piyo
eggs-ham-spam
```

`print`以外では`printf`も使える。C言語の`printf`のように、出力するフォーマットと出力する値（ここでは列数）を指定して実行できる。

出力する行を限定したい場合、`NR==`と指定することで出力する行数を指定できる。その際、`;`で区切ると条件が並列処理されるが、区切らない場合は直列処理される。`'NR==2;{print $1}'`であれば「2行目を出力しろ、それから全行の1列目を出力しろ」という処理になるが、`'NR==2{print $1}'`であれば「2行目を対象とし1列目を出力しろ」となるので2行目の1列目のみ出力される。なお、単純に`print`文の中に`NR`と記述すると、行数を表示してくれる。

出力したい条件に文言を指定する場合は`/hoge/`と指定する。この場合、hogeという文字列を含む行全てが出力対象になる。

```console
$ cat hoge.txt
1 2 3
4 5 6
10 11 12
$ awk '{print $1 * $2 * $3;}' hoge.txt
6
120
1320
$ awk '{print NR ":" $1 * $2 * $3;}' hoge.txt
1:6
2:120
3:1320
```

`awk`では簡単な計算もできる。

上記のような数値のみを一定のルールに沿ってで記述するファイルの場合、各列を入力とする四則演算が可能。

```console
$ awk '{print NR-1 ":" $1 * $2 * $3;}' hoge.txt
0:6
1:120
2:1320
```
ちなみに、行番号の表示を0から始めたい場合は`NR-1`と指定する。

```console
$ cat hoge.txt
1 2
3 4 5
6 7 8 9
$ awk '{print NR ":" $1 * $2 * $3;}' hoge.txt
1:0
2:60
3:336
$ awk '{print NR ":" $1 * $2 * $3 * $4;}' hoge.txt
1:0
2:0
3:3024
```

じゃあ、ファイルが持つ項目列が、`awk`で指定した列とは異なり多かったり少なかったりする場合はどうなるか。結論から言うと、ファイル側の項目列が足りない場合はその列を0とする。ファイルの1行目が「1 2」となっているとき、`awk`で`$1 * $2 * $3`という計算を実行すると`1 * 2 * 0`という計算を行うため、結果が0になる。逆にファイル側の項目列が多い場合は、`awk`で指定された列のみを計算する。ファイルの3行目が「6 7 8 9」となっているとき、`awk`で`$1 * $2 * $3`という計算を実行すると`6　* 7 * 8`という計算を行うため、結果が336になる。全項目を計算するには、`awk`で`$1 * $2 * $3 * $4`という計算を指定する必要があり、このあたりが「一定のルールに沿って」という但し書きが付く原因。

```console
$ cat hoge.txt
1 2
3 4 5
foo 6
7 bar
8 9 baz
$ awk '{print NR ":" $1 * $2 * $3;}' hoge.txt
1:0
2:60
3:0
4:0
5:0
$ awk '{print NR ":" $1 * $2;}' hoge.txt
1:2
2:12
3:0
4:0
5:72
$ awk '{print NR ":" $2 + 3;}' hoge.txt
1:5
2:7
3:9
4:3
5:12
```

今度は、ファイル内に数字と文字列を含む場合を考える。この場合は、文字列を0と見なすようだ。ファイル側の3行目は「foo 6」であるが、`$1 * $2`を計算すると`0 * 6`となり結果は0になる。あるいは`$2 + 3`だと結果は9（`6+3`）になる。上記のように、計算対象が数値と文字列を含むようなファイルであったとしても、プログラミング言語のように「データ型が違うぞ」みたいな感じのエラーにはならないので、そこだけは注意が必要。

```console
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

`print`文の中で`FILENAME`と書くと、処理対象のファイル名が出力される。

```console
 ls | grep ".*.txt" | xargs awk 'FILENAME=="hoge.txt"{print FILENAME ":" $1 " " $2 " " $3 " ans: " $1 + $2 + $3;}'
hoge.txt:1 2  ans: 3
hoge.txt:3 4 5 ans: 12
hoge.txt:6 7 8 ans: 21
hoge.txt:10   ans: 10
```

`FILENAME==ファイル名`とすると、処理対象のファイルを指定できる。

## reference

1. [awk](https://ja.wikipedia.org/wiki/AWK)
2. [とほほのAWK入門](http://www.tohoho-web.com/ex/awk.html)
3. [awkコマンド](https://hydrocul.github.io/wiki/commands/awk.html)

