# PythonからMongoDB Atlasにアクセスする

## detail

PythonからMongoDB Atlasにアクセスしてデータを取得する方法について記述する。将来的にはAPIサーバーとして運用することを想定して、Flaskをついでに導入しておくことにする。

### 前提

ここでは下記の処理が済んでいることを前提としている。

- MongoDB側にクラスターとデータベース、コレクションが既存である。
- Read権限あるいはReadとWrite権限を持ったユーザーを作成している。

プロジェクトのトップページから左メニュー内の「Database Access」を選択すると、ユーザーの一覧が表示される。ここで「Add new database user」ボタンをクリックすると、新しいユーザーを追加できる。ここの権限設定で「Only read any database」を選択しておけば、プロジェクト内におけるどのデータベースでもRead権限でアクセスできるようになる。

単純に接続確認をするだけなら初期ユーザーで行えばいいが、初期ユーザーはAdmin権限なのでただ接続確認するだけなら大げさだ。それに、恒常的に利用することを鑑みると、専用のユーザーを作成しておくほうが望ましいと思われる。ここではRead権限のみを持つchrisというユーザーを作成しておいた。

### 手順

1. Pythonの環境を準備する。
2. `pip`で必要なライブラリをインストールする。
3. MongoDB Atlasで接続用のドライバーを発行する。
4. Pythonスクリプトを用意する。
5. 接続確認を行う。

### Pythonの環境を準備する

Python自体の環境は、Dockerやローカルなど好きな方法で準備すればいい。後述する接続用ドライバーはPythonのバージョンで内容が異なるが、あまりに古いバージョンをあえて利用しなければならない場合を除けば、安定版を準備するべきと思う。どっちかって言うと注意が必要なのは、後述するMongoDBへの接続用のライブラリ。

### `pip`で必要なライブラリをインストールする

MongoDBへ接続するのに必要なライブラリは`pymongo`というもの。ところがこれをインストールする際には、ちょっとした記述が必要になる。

```console
python -m pip install pymongo[snappy,gssapi,srv,tls]
あるいは
pip3 install pymongo[srv]
pip install pymongo[srv]
```

角カッコの中身なに？となるが、この中で最低でも`srv`は必要。`pip install pymongo`とだけ書くと、あとで実行したときに「dnspython must be installed error」という[わかりにくいエラーになってしまう](https://stackoverflow.com/questions/52930341/pymongo-mongodbsrv-dnspython-must-be-installed-error)。`pip`でこのように角カッコを記述するのは、**インストール対象のライブラリについて環境を指定したい**場合。つまり単純に`pymongo`とするだけではダメで、`srv`用環境としての`pymongo`が必要なわけだ。

とりあえず、最低限接続に必要なライブラリはこれだけだが、後々APIサーバーとして扱いたいのでFlaskもインストールしておこう。

### MongoDB Atlasで接続用のドライバーを発行する

MongoDBには、いわゆるAPI Keyとは異なる接続用のドライバーが存在する。

![pic](2021-02-28-22-08-26.png)

まずはとりあえず、いつもどおりMongoDB Atlasにログインする。次に接続したいクラスタの「CONNECT」ボタンを押す。

![pic](2021-02-28-22-09-42.png)

表示されたメニューのうち、真ん中の「Connect your application」を押す。

![pic](2021-02-28-22-11-06.png)

次に表示されたメニューから、接続元の環境を選択する。ここではPythonの3.6移行を選択した。すると、`mongodb+srv://`で始まる文字列が表示されると思うので、この文字列を丸々コピーする。後述するPythonスクリプトに記述するのだが、ここで編集が必要になるのは下記の項目。

- ユーザー名。これは`<username>`となっている部分を書き換える。もちろん`<>`の部分もだぞ！
- パスワード。これは`<password>`となっている部分を書き換える。もちろん`<>`の部分もだぞ！
- 接続先データベース名。これは`myFirstDatabase`となっている部分を書き換える。

> Replace <password> with the password for the <username> user. Replace myFirstDatabase with the name of the database that connections will use by default.

まぁこのページ内で上記のように注意書きされているので、書き換え忘れることはないと思うが。

### Pythonスクリプトを用意する

Pythonスクリプトは下記の内容であれば、接続して値を取得できる。

```python
from flask import Flask
from flask_cors import CORS
import pymongo

connection_url = 'mongodb+srv://<USERNAME>:<PASSWORD>@hoge.fuga.mongodb.net/DATABASE_NAME?retryWrites=true&w=majority'

app = Flask(__name__)
client = pymongo.MongoClient(connection_url)

db = client.DATABASE_NAME
collection = db.COLLECTION_NAME

result = collection.find_one()
print(result)

if __name__ == '__main__':
    app.run(debug=True)

```

前述の通りだが、まずMongoDBに接続するドライバーの部分で書き換える必要があるのは3箇所。

- ユーザー名
- パスワード
- 接続先データベース名

さらに接続が確立したあとは、データを取得したいコレクションを指定して実際に値を取得する必要がある。

```python
db = client.DATABASE_NAME
collection = db.COLLECTION_NAME

result = collection.find_one()
print(result)
```

それをしているのが上記の部分。ここで`DATABASE_NAME`となっている部分には**実際のデータベース名を記述する**。データベース名が`hoge`という名前なら、上記のスクリプトなら`db.hoge`と記述すれば参照先データベースを指定できる。

なお、接続用ドライバーである`connection_url`の中で接続先データベース名を指定しているが、これは**デフォルトで使用するデータベース名**を記述している。クラスタ内にデータベースが複数あって、デフォルトでアクセスする先と実際に値を取得したい先が異なる場合などは上記のように接続先を逐一指定する。じゃあ、デフォルトの接続先と値の取得先が同じなら`client.DATABASE_NAME`の部分は不要なんじゃ？と思って`client.COLLECTION_NAME`と記述して実行してみたが、`find_one()`のところで「'Collection' object is not callable.」エラーになってしまった。そんなわけで、現状では上記の冗長な書き方をしている。

`COLLECTION_NAME`には、値の取得を実行したいコレクション名を記述する。コレクション名が`fuga`という場合、上記のスクリプトなら`db.fuga`と記述すれば参照先コレクションを指定できる。

`find_one()`は、とりあえずなんか1つデータくれという意味なので、検索する値を設定する場合は`find_one({'KEY': 'VALUE'})`と記述する。`find_one()`とだけ記述して実行する場合、最初のデータが参照される。

### 接続確認を行う

ここまでスクリプトを書いたら実行してみる。結果がコンソールに出力されるはず。ここまでできれば、あとは「GETあるいはPOSTされたらMongoDBにアクセスして値を返す」ようなAPIサーバーとして記述しれやればいい。

## reference

1. [Connect to Your Cluster](https://docs.atlas.mongodb.com/tutorial/connect-to-your-cluster/)
2. [Insert and View Data in Your Cluster](https://docs.atlas.mongodb.com/tutorial/insert-data-into-your-cluster/)
3. [Make Python API to access Mongo Atlas Database](https://www.geeksforgeeks.org/make-python-api-to-access-mongo-atlas-database/)
4. [pymongo - mongodb+srv “dnspython must be installed” error](https://stackoverflow.com/questions/52930341/pymongo-mongodbsrv-dnspython-must-be-installed-error)
5. [Python: 環境ごとの依存ライブラリをセットアップスクリプトの extras_require で管理する](https://blog.amedama.jp/entry/2016/06/17/224532)
