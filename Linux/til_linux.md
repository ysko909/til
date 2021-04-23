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
