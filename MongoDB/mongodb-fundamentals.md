# MongoDBについて

## detail

[MongoDB](https://www.mongodb.com/)とは、NoSQLと呼ばれるタイプの構成を持つデータベースシステム。データ（レコード）をテーブルに格納するRDBMSと異なり、「ドキュメント」と呼ばれる構造的データをJSONライクな形式で表現し、そのドキュメントの集合体を「コレクション」として管理する。

NoSQLはその構造上、RDBMSよりもデータの追加・更新・削除などは高速に行える。また、扱うデータの特性によってはRDBMSよりも容易かつ迅速に開発できる特徴がある。また、冗長性に優れており、スケールアウトも容易である。

逆に、リレーショナルな構造を取ることができないため、RDBMSでは可能な結合操作（`join`のような操作）が行えない。そして、"NoSQL"であるためそもそもSQLが利用できない。また、トランザクション処理には一定の条件があり、RDBMSと同等に利用できるわけではない。

上記のような特徴を持つため、「大量のデータを処理したい」「スモールスタートしつつデータの増加に合わせてスケールアウトする」などのニーズに対応するため、webアプリの情報分析やIoTのデータ解析などに用いられる。

## reference

1. [MongoDB](https://www.mongodb.com/)
2. [やってみようNoSQL　MongoDBを最速で理解する](https://qiita.com/Brutus/items/8a67a4db0fdc5a33d549)
3. [MongoDBとは](https://www.designet.co.jp/faq/term/?id=TW9uZ29EQg)
