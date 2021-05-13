# コンテナ内で作成したファイルをホストで操作する場合権限でエラーになるケースがある

## detail

`VOLUME`を使ってホストのフォルダをコンテナにマウントすることで、コンテナ内でホストのファイルを使ったり、コンテナ内で作成したファイルをホストで操作できる。ただし、後者の場合はファイルのowner権限を考慮しておかないと問題が起きる。

```console
sampleusr@foohost:~/foo$ id
uid=1000(sampleusr) gid=9999(testgr) groups=9999(testgr),999(docker)
sampleusr@foohost:~/foo$ mkdir hoge && touch hoge/hoge.txt
sampleusr@foohost:~/foo$ ls -al hoge
total 8
drwxr-xr-x 2 sampleusr testgr 4096  4月 20 16:41 .
drwxr-xr-x 4 sampleusr testgr 4096  4月 20 16:41 ..
-rw-r--r-- 1 sampleusr testgr    0  4月 20 16:41 hoge.txt
sampleusr@foohost:~/foo$ docker run -it -v $(pwd)/hoge:/hoge busybox
/ # ls -al /hoge
total 8
drwxr-xr-x    2 1000     9999          4096 Apr 20 07:41 .
drwxr-xr-x    1 root     root          4096 Apr 20 07:42 ..
-rw-r--r--    1 1000     9999             0 Apr 20 07:41 hoge.txt
/ # touch /hoge/foo.txt
/ # ls -al /hoge
total 8
drwxr-xr-x    2 1000     9999          4096 Apr 20 07:43 .
drwxr-xr-x    1 root     root          4096 Apr 20 07:42 ..
-rw-r--r--    1 root     root             0 Apr 20 07:43 foo.txt
-rw-r--r--    1 1000     9999             0 Apr 20 07:41 hoge.txt
/ # exit
sampleusr@foohost:~/foo$ ls -al hoge
total 8
drwxr-xr-x 2 sampleusr testgr 4096  4月 20 16:43 .
drwxr-xr-x 4 sampleusr testgr 4096  4月 20 16:41 ..
-rw-r--r-- 1 root      root      0  4月 20 16:43 foo.txt
-rw-r--r-- 1 sampleusr testgr    0  4月 20 16:41 hoge.txt
sampleusr@foohost:~/foo$ rm hoge/foo.txt
rm: remove write-protected regular empty file 'hoge/foo.txt'? y
rm: cannot remove 'hoge/foo.txt': Permission denied
```

コンテナ内で作成したファイル`foo.txt`はownerがrootになっているため、そのファイルをホストで削除しようとしても（ホストでは一般ユーザーとしてログインしているので）Permission denidとメッセージが出力される。

```console
sampleusr@foohost:~/foo$ mkdir hoge
sampleusr@foohost:~/foo$ docker run -it -v $(pwd)/hoge:/hoge busybox
/ # mkdir -p /hoge/fuga/piyo
/ # exit
sampleusr@foohost:~/foo$ ls -al hoge
total 12
drwxr-xr-x 3 sampleusr testgr 4096  4月 21 13:19 .
drwxr-xr-x 4 sampleusr testgr 4096  4月 21 13:18 ..
drwxr-xr-x 3 root      root   4096  4月 21 13:19 fuga
sampleusr@foohost:~/foo$ touch hoge/fuga/piyo/foo.txt
touch: cannot touch 'hoge/fuga/piyo/foo.txt': Permission denied
```

他のケースとしては、コンテナ内で作成されたフォルダに対しファイルを作成しようとすると権限エラーが起きる。フォルダのownerがrootになっているため、ホスト側でログインしている一般ユーザーの権限では足りないからだ。もちろんフォルダの削除もできない。

この現象は、どうもLinuxのホストでLinuxのコンテナを利用するときに発生するようで、ホストがWindowsかmacOSの場合は発現しなかった。

解決方法はいくつかあるが、簡単なのはコンテナ内で`useradd`して一般ユーザーを作成し、作成した一般ユーザーで処理を実行するというもの。`useradd`する際に、ホスト側のユーザーに関するUIDやGIDを渡してやればいい。あるいは、Dockerコンテナ起動時に`-u`オプションで起動時のUIDやGIDをコンテナに引き継ぐ方法もある。ただし、後者の方法は`/etc/passwd`と`/etc/group`をコンテナにボリュームマウントする必要がある。

## reference

1. [dockerでvolumeをマウントしたときのファイルのowner問題](https://qiita.com/yohm/items/047b2e68d008ebb0f001)
2. [Docker コンテナ内で Docker ホストと同じユーザを使う](https://blog.amedama.jp/entry/docker-container-host-same-user)
