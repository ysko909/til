# PostgreSQLに接続する

## detail

PythonでPostgreSQLに接続するにはいろいろ方法はあるが、ここでは`psycopg2`を使った方法を記述する。

```console
pip install psycopg2
```

上記コマンドを実行して、モジュールをインストールしておく。

```python
>>> import psycopg2
>>> cnn = psycopg2.connect('host=xxx.xxx.xxx.xxx port=xxxx dbname=pg_work user=hoge_admin password=hoge_admin')
>>> cur = cnn.cursor()
>>> cur.execute('select tablename from pg_tables;')
>>> cur.fetchone()
...省略
>>> cur.execute('select * from mss_haigo;')
>>> cur.fetchone()
...省略
>>> cur.close()
>>> cnn.close()
```

一番簡単な接続確認だと、上記のようになると思う。

`select tablename from pg_tables;`は、DBが保持しているテーブルの一覧を取得する。`pg_tables`はPostgreSQLが持つシステムテーブルなので、どんな環境でもこの名前で取得できるはず。`fetchone()`は文字通り、最初の1件目を取得して表示する。

## reference

1. [psycopg](https://www.psycopg.org/)
2. [psycopg2](https://pypi.org/project/psycopg2/)
3. [Pythonから各種DBへ接続する方法（PEP 249）とSQLAlchemyについて](https://qiita.com/overflowfl/items/5abdf49322942276fb2c)
4. [PythonでPostgreSQLに接続してみた](http://h2shiki.hateblo.jp/entry/2016/05/05/210738)
5. [PostgreSQLでテーブルの一覧を取得する方法](https://lightgauge.net/database/postgresql/get-table-list)
6. [PostgreSQLトランザクションとDBD::Pgの謎の挙動](https://adiary.adiary.jp/0257)
