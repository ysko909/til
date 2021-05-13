# `grep`でOr検索したい場合は`-e`オプションをつける

## detail

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

## reference

1. [grepで複数の文字列を検索する方法（OR検索）](https://ex1.m-yabe.com/archives/2826)
