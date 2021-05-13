# 行頭を出力するときは`head`、行末を出力するときは`tail`を使う

## detail

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

## reference

1. [14 tail and head commands in Linux/Unix](https://www.linux.com/training-tutorials/14-tail-and-head-commands-linuxunix/)
2. [ファイルのn行目以降を表示する](https://qiita.com/sugyan/items/523ed9417678fbdbae53)
