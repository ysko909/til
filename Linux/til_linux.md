# TIL - Linux

Linuxのまとめ。

## grepで該当する行の前後行を出力したいときは-Aと-B

### detail

`grep`利用時、そのままだと指定した文字列に該当する行だけしか表示されない。該当する行を含めてその前後行も表示したい場合は、オプションの`-A`と`-B`を利用する。

```console
$ cat requirements.txt | grep pandas
pandas
```

`grep`のみだと該当する行だけ出力される。

```console
$ cat requirements.txt | grep pandas -A 3
pandas
matplotlib
seaborn
tqdm
$ cat requirements.txt | grep pandas -B 3
numpy
pandas
$ cat requirements.txt | grep pandas -B2 -A1 
numpy
pandas
matplotlib
```

`-A`だと該当する行の後続行、`-B`だと該当する行より先行する行をそれぞれ指定された数値分出力する。ただし、たとえば該当する行の後続行は1行しかない場合は、`-A10`と指定しても後続の1行しか出力されない。

### reference

1. [grepで該当する行の前後も一緒に表示するには](https://www.atmarkit.co.jp/flinux/rensai/linuxtips/137greplineplus.html)

## ユーザーとグループの一覧を表示する方法

### detail

ユーザーとグループの一覧を出力する単体のコマンドは存在しないが、ユーザ一覧を取得して`groups`コマンドに渡すことで、ユーザ名とグループ名の一覧が取得できる。

> compgen -u | xargs -I@ groups @

### reference

1. [Linuxでユーザ名・所属グループ名(プライマリ・サブ)の一覧を取得する](https://orebibou.com/ja/home/201705/20170509_001/)

## 行頭を出力するときは`head`、行末を出力するときは`tail`を使う

### detail

ファイルやコマンドの出力結果のうち、行頭だけ欲しい場合は`head`を使う。逆に、行末だけ欲しい場合は`tail`を使う。

```console
head /etc/group
tail /etc/group
ls -al | head
ls -al | tail
```

デフォルトでは`head`も`tail`も10行分出力する。

```console
/home/hoge # cat hoge.txt
line1
line2
line3
line4
line5
line6
line7
line8
line9
line10
/home/hoge # head -n 3 hoge.txt
line1
line2
line3
/home/hoge # head -n -3 hoge.txt
line1
line2
line3
line4
line5
line6
line7
/home/hoge # head -n +3 hoge.txt
head: invalid number '+3'
/home/hoge # tail -n 3 hoge.txt
line8
line9
line10
/home/hoge # tail -n -3 hoge.txt
line8
line9
line10
/home/hoge # tail -n +3 hoge.txt
line3
line4
line5
line6
line7
line8
line9
line10
```

出力する行数を指定する場合は、`-n`オプションをつけたあと行数を指定する。さらに、数字にプラスあるいはマイナスを付与することで挙動が変わり、`tail -n +3`「3行目以降を出力する」という処理が可能になる。ただし、上記はDockerのbusyboxコンテナで実行しているが、`head -n +3`でエラーになっている。ところが、Ubuntuだと実行できる（この場合、最初の行から2行目までを表示する`head -n 3`と同じ挙動）ので、ディストリビューションやバージョンによって挙動が異なるっぽい。

### reference

1. [14 tail and head commands in Linux/Unix](https://www.linux.com/training-tutorials/14-tail-and-head-commands-linuxunix/)
2. [ファイルのn行目以降を表示する](https://qiita.com/sugyan/items/523ed9417678fbdbae53)

## `awk`を使う

### detail

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

出力する行を限定したい場合、`NR==`と指定することで出力する行数を指定できる。その際、`;`で区切ると条件が並列処理されるが、区切らない場合は直列処理される。`'NR==2;{print $1}'`であれば「2行目を出力しろ、それから全行の1列目を出力しろ」という処理になるが、`'NR==2{print $1}'`であれば「2行目を対象とし1列目を出力しろ」となるので2行目の1列目のみ出力される。

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

### reference

1. [awk](https://ja.wikipedia.org/wiki/AWK)
2. [とほほのAWK入門](http://www.tohoho-web.com/ex/awk.html)
3. [awkコマンド](https://hydrocul.github.io/wiki/commands/awk.html)

## `grep`でOr検索したい場合は`-e`オプションをつける

### detail

```console
/home/hoge # cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
kmem:x:9:
wheel:x:10:root
cdrom:x:11:
dialout:x:18:
floppy:x:19:
video:x:28:
audio:x:29:
tape:x:32:
www-data:x:33:
operator:x:37:
utmp:x:43:
plugdev:x:46:
staff:x:50:
lock:x:54:
netdev:x:82:
users:x:100:
nobody:x:65534:
/home/hoge # cat /etc/group | grep -e tt -e ad
adm:x:4:
tty:x:5:
/home/hoge # cat /etc/group | grep -e tt -e da
daemon:x:1:
tty:x:5:
www-data:x:33:
/home/hoge # cat /etc/group | grep -e tt -e da -e pe
daemon:x:1:
tty:x:5:
tape:x:32:
www-data:x:33:
operator:x:37:
```

`-e`オプションをつけて列挙することで、それらがOr条件として処理される。なお、`-i`などのオプションは、`grep -i -e a -e b -e c`のように`-e`より先に記述しておくと見やすいと思う。

### reference

1. [grepで複数の文字列を検索する方法（OR検索）](https://ex1.m-yabe.com/archives/2826)

## `which`が使えないなら`type`を使えばいいじゃない

### detail

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

### reference

1. [type](https://ss64.com/osx/type.html)
2. [Why “which” command does not give you the correct path?](https://apple.stackexchange.com/questions/73965/why-which-command-does-not-give-you-the-correct-path)

