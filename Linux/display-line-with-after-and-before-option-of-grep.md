# grepで該当する行の前後行を出力したいときは-Aと-B

## detail

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

## reference

1. [grepで該当する行の前後も一緒に表示するには](https://www.atmarkit.co.jp/flinux/rensai/linuxtips/137greplineplus.html)
