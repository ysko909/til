
# Jupyter Notebookで`print`出力を省略させない

## detail

Notebookを使っている際、行数の多いDataFrameやSeriesを`print`などで表示させると、途中が「・・・」と省略されて出力されることがある。単純に数行見るだけなら`head`や`tail`を使うので、`print`したときは全行出力してもらいたい。そんなときはpandasの設定を変更する。

```python
pandas.set_option('display.max_rows', 400)
```

pandasの設定を変更するには、`set_option`を利用する。第1引数に変更したい項目を、第2引数に設定する値を記述する。これで、第1引数に指定したオプションの値が変更される。ここでは`display.max_rows`というオプションを指定しているが、この値より多い行数を表示しようとすると、前述のとおり省略されてしまう。たとえば出力対象行が200行以上あった場合、デフォルトでは冒頭の5行と末尾の5行が表示されるが、途中の行は「・・・」として省略される。`display.max_rows`を200に設定すれば、省略されずに全行が出力される。

```python
dir(pd.options.display)
```

ちなみに、`display.max_rows`以外にも背って項目は存在し、上記のコマンドを実行することで項目の一覧を取得できる。

## reference

1. [pandasで表示が省略されるのを防ぐ](https://uyamazak.hatenablog.com/entry/2016/09/29/163534)
