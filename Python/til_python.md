# TIL - Python

Pythonに関することはここに書く。

## マルコフ連鎖用ライブラリMarkovify

### detail

[Markovify](https://github.com/jsvine/markovify)は、マルコフ連鎖を実装するライブラリ。マルコフ連鎖に特化したライブラリなので何でもできるライブラリではないものの、実装するだけならライブラリをインポートするだけでいいので楽。

### reference

1. [Markovify + Mecabによるマルコフ連鎖を使った文章生成](https://dai-py.hatenablog.com/entry/2019/04/09/023416)
1. [markovifyを使ってマルコフ連鎖でなろう小説のタイトルを自動生成するぞ！](https://murabitoleg.com/markov-narou/)
1. [マルコフ連鎖を使って〇〇っぽい文章を自動生成してみた](https://www.pc-koubou.jp/magazine/4238)
1. [[Python] MeCab とマルコフ連鎖ライブラリ markovify を使い、文章を学習して自動生成する方法](https://qiita.com/shge/items/fbfce6b54d2e0cc1b382)
1. [N階マルコフ連鎖によるキャラクターのセリフ自動生成器を作ってみた](https://qiita.com/takaito0423/items/1a39d790b5e5b8e4cf36)

## 日本語専用形態素解析器Sudachi

### detail

日本語の形態素解析器だと[mecab](https://taku910.github.io/mecab/)が有名どころだが、[Sudachi](https://github.com/WorksApplications/SudachiPy)はワークスアプリケーションズで開発された、日本語用形態素解析器。辞書もPythonのライブラリ扱いされており、`pip`インストールできる。

```console
$ echo "アルミ缶の上にあるミカン" | sudachipy
アルミ缶        名詞,普通名詞,一般,*,*,*        アルミ缶
の      助詞,格助詞,*,*,*,*     の
上      名詞,普通名詞,副詞可能,*,*,*    上
に      助詞,格助詞,*,*,*,*     に
ある    動詞,非自立可能,*,*,五段-ラ行,連体形-一般       有る
ミカン  名詞,普通名詞,一般,*,*,*        蜜柑
EOS
```

`pip`でのインストールが終わると、コマンドライン上から利用できるようになる。

```python
from sudachipy import tokenizer
from sudachipy import dictionary

tokenizer_obj = dictionary.Dictionary().create()

s = '日本経済新聞は、日本経済新聞社の発行する新聞であり、広義の全国紙の一つ。略称は日経、または日経新聞。'

mode = tokenizer.Tokenizer.SplitMode.A
result = [m.surface() for m in tokenizer_obj.tokenize(s, mode)]

print(result)
# ['日本', '経済', '新聞', 'は', '、', '日本', '経済', '新聞', '社', 'の', '発行', 'する', '新聞', 'で', 'あり', '、', '広義', 'の', '全国', '紙', 'の', '一', 'つ', '。', '略称', 'は', '日経', '、', 'また', 'は', '日経', '新聞', '。']
```

Python上で利用するには、上記のようなコードになる。

### reference

1. [SudachiPy](https://github.com/WorksApplications/SudachiPy)
1. [Pythonで形態素解析器Sudachiを使う](https://ohke.hateblo.jp/entry/2019/03/09/101500)

## Pythonで時系列データのテストデータを作成する方法について

### detail

pandasとnumpyを使って時系列データのテストデータを作るには、`date_range()`を使って月次や日次のデータを作る。そのデータをインデックスにして、DataFrameを生成するとテスト用の時系列データが生成できる。データ生成の間隔を指定することで、月次や1時間ごとなどの指定が可能。

```python
>>> import pandas as pd
>>> dates = pd.date_range('2020/1/1', '2020/3/31', freq='D')
>>> len(dates)
91
>>> import numpy as np
>>> df = pd.DataFrame(np.arange(len(dates) * 3).reshape(len(dates), 3), columns=['hoge', 'fuga', 'piyo'], index=dates)
>>> df.head()
            hoge  fuga  piyo
2020-01-01     0     1     2
2020-01-02     3     4     5
2020-01-03     6     7     8
2020-01-04     9    10    11
2020-01-05    12    13    14
>>> 
```

インデックスには、生成した日付の連続値を設定する。項目列側には、`np.arange(len(dates) * 3)`で生成した`91 * 3`個のデータを、各列にそれぞれハマるように`reshape()`して格納する。列名は適当に設定。これだけで、日次のデータ群が生成できる。

```python
>>> pd.date_range('2020/1/1', '2020/1/2', freq='H')
DatetimeIndex(['2020-01-01 00:00:00', '2020-01-01 01:00:00',
               '2020-01-01 02:00:00', '2020-01-01 03:00:00',
               '2020-01-01 04:00:00', '2020-01-01 05:00:00',
               ...
               '2020-01-01 20:00:00', '2020-01-01 21:00:00',
               '2020-01-01 22:00:00', '2020-01-01 23:00:00',
               '2020-01-02 00:00:00'],
              dtype='datetime64[ns]', freq='H')
>>> pd.date_range('2020/1/1 00:00:00', '2020/1/1 01:00:00', freq='min')
DatetimeIndex(['2020-01-01 00:00:00', '2020-01-01 00:01:00',
               '2020-01-01 00:02:00', '2020-01-01 00:03:00',
               '2020-01-01 00:04:00', '2020-01-01 00:05:00',
               '2020-01-01 00:06:00', '2020-01-01 00:07:00',
                ...
               '2020-01-01 00:54:00', '2020-01-01 00:55:00',
               '2020-01-01 00:56:00', '2020-01-01 00:57:00',
               '2020-01-01 00:58:00', '2020-01-01 00:59:00',
               '2020-01-01 01:00:00'],
              dtype='datetime64[ns]', freq='T')
>>> pd.date_range('2020/1/1', '2020/3/31', freq='M')
DatetimeIndex(['2020-01-31', '2020-02-29', '2020-03-31'], dtype='datetime64[ns]', freq='M')
```

`freq`に指定可能な値はいろいろあるが、注意が必要なのは`M`でこれは**月次**を表す。**分ではない**ので注意。分ごとのデータを生成したい場合は、`min`か`T`を指定する。分の指定が`M`だと、Month（月）が重複しちゃうってのはわかる。だから`min`っていう識別子があるんだなって理解はとりあえずできるしわかりやすい。それはそうと、`T`って何だろうなー。minuteの語尾かな・・・。ちなみに、上記で使っている`reshape()`は、配列の形状を変更するのに利用する。

```python
>>> np.arange(12)
array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11])
>>> np.arange(12).reshape(4, 3)
array([[ 0,  1,  2],
       [ 3,  4,  5],
       [ 6,  7,  8],
       [ 9, 10, 11]])
>>> np.arange(12).reshape(2, 6)
array([[ 0,  1,  2,  3,  4,  5],
       [ 6,  7,  8,  9, 10, 11]])
>>> np.arange(12).reshape(1, 12)
array([[ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11]])
>>> np.arange(12).reshape(12, 1)
array([[ 0],
       [ 1],
       [ 2],
       [ 3],
       [ 4],
       [ 5],
       [ 6],
       [ 7],
       [ 8],
       [ 9],
       [10],
       [11]])
```

もともとの形状（ここでは1次元の12要素）から変更できるなら、どんな形状にでも変更できる。2x6だったり4x3だったりと自由自在。

```python
>>> np.range(12).reshape(3, 3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/lib/python3.8/site-packages/numpy/__init__.py", line 214, in __getattr__
    raise AttributeError("module {!r} has no attribute "
AttributeError: module 'numpy' has no attribute 'range'
```

上記のように、もともとの形状と変更先の形状で要素数が異なる（もともとの要素数は12だが、`reshape()`で指定しているのは`3, 3`の9で一致しない）場合はエラーになる。

### reference

1. [pandasの時系列データにおける頻度（引数freq）の指定方法](https://note.nkmk.me/python-pandas-time-series-freq/)
1. [pandas.DataFrameの構造とその作成方法](https://note.nkmk.me/python-pandas-dataframe-values-columns-index/)
1. [pandas で日時の連続値を生成し、インデックスに使う](http://ailaby.com/date_range/)

## Pythonのミュータブルとイミュータブルについて

### detail

Pythonのミュータブルとイミュータブルの違いについて。
  - ミュータブルとは変更が可能なデータ型のことを指す。リスト、タプルなんかが代表例。
  - イミュータブルとは変更が不可能なデータ型のことを指す。int、float、strなどが代表例。
イミュータブルとは変更不可という意味だが、**変数に対する代入操作で変更不可という意味ではない**ので注意が必要。

```python
>>> a = 1
>>> a = 2
>>> a += 3
>>> a
5
>>> type(a)
<class 'int'>
```

- 上記のように、変数`a`はintなのでイミュータブルであるが、変数に対する操作で格納する値は変更されている。じゃあ何がイミュータブルなんだというと、**同一のidを指し示したままの変更は不可**という意味である。

```python
>>> a = 1
>>> id(a)
139825682166016
>>> a = 2
>>> id(a)
139825682166048
>>> a += 3
>>> a
5
>>> id(a)
139825682166144
>>> type(a)
<class 'int'>
```

- `id()`関数は[オブジェクトの識別子](https://docs.python.org/ja/3/library/functions.html#id)を返す。
- 上記の通り、見た目は変数`a`のみ登場しているのに、その識別子はそれぞれ異なっている。これは、変数`a`がその都度異なるオブジェクトを、その都度参照しているだけであることを示している。

```python
>>> a = 1
>>> b = 2
>>> c = 3
>>> hoge = [a, b, c]
>>> for h in hoge:
...   print(h + 1)
... 
2
3
4
>>> for h in hoge:
...   h = h + 2
...   print(h)
... 
3
4
5
>>> hoge
[1, 2, 3]
>>> a
1
```

- 上記は、イミュータブルなオブジェクトをミュータブルなリストの要素にした。要素に対し加算した場合、出力される結果そのものは加算された結果だが、肝心の要素側に変化はない。
- この加算された結果も新しいオブジェクトとして生成されており、変数`h`はそれらのオブジェクトを順に参照しているだけに過ぎない。よって、`for`のロープが終わったあとで`h`を参照すると、5のオブジェクトを指し示している。
- これらのことから、`for`で利用される変数`h`は新しい変数として作成され、リストの要素を順に参照する。それらの値に何かしらの処理を行った場合、それぞれが新しいオブジェクトとして生成され、そのオブジェクトを変数`h`が参照するような仕組み。
- よって、上記のようなfor文によるリストの各要素への処理を実行した場合、リストの要素そのものが変更されるわけではない。また、各要素を参照した変数（ここで言うと`h`）はループを抜けたあとだと、最後の要素に対する処理を実行した後の値（ここでは3 + 2の処理結果である5）を保持している。

```python
>>> a = 1
>>> b = 2
>>> c = 3
>>> hoge = [a, b, c]
>>> id(hoge)
4432487280
>>> for h in hoge:
...   print('{}: {}'.format(h, id(h)))
... 
1: 4430876432
2: 4430876464
3: 4430876496
>>> for h in hoge:
...   h = h + 2
...   print('{}: {}'.format(h, id(h)))
... 
3: 4430876496
4: 4430876528
5: 4430876560
>>> hoge
[1, 2, 3]
>>> for h in hoge:
...   print('{}: {}'.format(h, id(h)))
... 
1: 4430876432
2: 4430876464
3: 4430876496
>>> d = 4
>>> hoge.append(d)
>>> hoge
[1, 2, 3, 4]
>>> id(hoge)
4432487280
>>> for h in hoge:
...   print('{}: {}'.format(h, id(h)))
... 
1: 4430876432
2: 4430876464
3: 4430876496
4: 4430876528
```

- `id()`関数を使って、リストと要素の識別子を参照してみた。
- 要素に対し加算した場合、加算結果はリスト内の要素とは別のオブジェクトを参照していることが、識別子が異なることからわかる。
- もともとのリストが参照する先は変更されていないため、リストの要素は加算処理の前後で変わらない。
- よって、「リストが持つ要素に変更は加えたくないが、何かしらの処理を要素に対して行いたい」という場合は、上記のコードで実現できる。

```python
>>> fuga = [1, 2, 3]
>>> id(fuga)
4548215664
>>> for f in fuga:
...   print('{}: {}'.format(f, id(f)))
...   f += 2
...   print('{}: {}'.format(f, id(f)))
... 
1: 4546588432
3: 4546588496
2: 4546588464
4: 4546588528
3: 4546588496
5: 4546588560
>>> id(fuga)
4548215664
>>> 
```

- ちなみに、リストの要素に変数ではなく直接int型の値を格納しても、挙動は一緒。
- [このページ](http://pythontutor.com/visualize.html)でPythonを書くと、そのオブジェクトをグラフィカルに表示してくれる。見た目でわかりやすいので、オブジェクト絡みで挙動を見てみたいときは使ってみるといいかもしれない。

### reference

1. [オブジェクト、値、および型](https://docs.python.org/ja/3/reference/datamodel.html)
1. [pythontutor](http://pythontutor.com/visualize.html)

## DataFrameの行や列で差分・変化率を取得する

### detail

pandasのDataFrameにおいて、行や列ごとの差分や変化率を取りたいケースがある。とくに時系列データを扱う際、1日ごとの値の変化率を取って1週間で平均したい場合などが考えられる。こういう場合、差分なら`deff()`で、変化率は`pct_change()`でそれぞれ取得できる。

```python
>>> import pandas as pd
>>> import io

>>> s = '''a,b,c
... 1,2,3
... 2,4,6
... 3,5,7
... 1,4,7
... 9,10,11
... '''
>>> df = pd.read_csv(io.StringIO(s))
>>> df
   a   b   c
0  1   2   3
1  2   4   6
2  3   5   7
3  1   4   7
4  9  10  11
```

上記のようなDataFrameに対し、差分と変化率を取得してみる。

まずは引数なしで実行する。

```python
>>> df.diff()
     a    b    c
0  NaN  NaN  NaN
1  1.0  2.0  3.0
2  1.0  1.0  1.0
3 -2.0 -1.0  0.0
4  8.0  6.0  4.0
>>> df.pct_change()
          a     b         c
0       NaN   NaN       NaN
1  1.000000  1.00  1.000000
2  0.500000  0.25  0.166667
3 -0.666667 -0.20  0.000000
4  8.000000  1.50  0.571429
```

引数を指定しない場合、1行前のデータとの差分および変化率を取得する。そのため、1行前のデータが存在しない1行目のデータ（行ラベル`0`のデータ）は、差分や変化率が算出できないため`NaN`になる。

```python
>>> df.diff(2)
     a    b    c
0  NaN  NaN  NaN
1  NaN  NaN  NaN
2  2.0  3.0  4.0
3 -1.0  0.0  1.0
4  6.0  5.0  4.0
>>> df.pct_change(2)
     a    b         c
0  NaN  NaN       NaN
1  NaN  NaN       NaN
2  2.0  1.5  1.333333
3 -0.5  0.0  0.166667
4  2.0  1.0  0.571429
>>> df.diff(-1)
     a    b    c
0 -1.0 -2.0 -3.0
1 -1.0 -1.0 -1.0
2  2.0  1.0  0.0
3 -8.0 -6.0 -4.0
4  NaN  NaN  NaN
>>> df.pct_change(-1)
          a     b         c
0 -0.500000 -0.50 -0.500000
1 -0.333333 -0.20 -0.142857
2  2.000000  0.25  0.000000
3 -0.888889 -0.60 -0.363636
4       NaN   NaN       NaN
```

引数に数値を指定した場合、その数値分前（マイナス値の場合は後）のデータと比較して、差分や変化率を取得する。マイナスの値も指定できる。この場合でも、比較対象のデータが存在しない場合は`NaN`になる。

```python
>>> df.diff(axis=1)
    a  b  c
0 NaN  1  1
1 NaN  2  2
2 NaN  2  2
3 NaN  3  3
4 NaN  1  1
>>> df.pct_change(axis=1)
    a         b     c
0 NaN  1.000000  0.50
1 NaN  1.000000  0.50
2 NaN  0.666667  0.40
3 NaN  3.000000  0.75
4 NaN  0.111111  0.10
>>> df.diff(2, axis=1)
    a   b  c
0 NaN NaN  2
1 NaN NaN  4
2 NaN NaN  4
3 NaN NaN  6
4 NaN NaN  2
>>> df.pct_change(2, axis=1)
    a   b         c
0 NaN NaN  2.000000
1 NaN NaN  2.000000
2 NaN NaN  1.333333
3 NaN NaN  6.000000
4 NaN NaN  0.222222
```

引数に`axis=1`を設定すると、列を対象に差分や変化率を取得する。数値を指定すれば、指定された列数分で比較する。

### reference

1. [pandas.DataFrame.diff](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.diff.html)
2. [pandas.DataFrame.pct_change](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.pct_change.html)
3. [pandasで行・列の差分・変化率を取得するdiff, pct_change](https://note.nkmk.me/python-pandas-diff-pct-change/)

## Jupyter Notebookで`print`出力を省略させない

### detail

Notebookを使っている際、行数の多いDataFrameやSeriesを`print`などで表示させると、途中が「・・・」と省略されて出力されることがある。単純に数行見るだけなら`head`や`tail`を使うので、`print`したときは全行出力してもらいたい。そんなときはpandasの設定を変更する。

```python
pandas.set_option('display.max_rows', 400)
```

pandasの設定を変更するには、`set_option`を利用する。第1引数に変更したい項目を、第2引数に設定する値を記述する。これで、第1引数に指定したオプションの値が変更される。ここでは`display.max_rows`というオプションを指定しているが、この値より多い行数を表示しようとすると、前述のとおり省略されてしまう。たとえば出力対象行が200行以上あった場合、デフォルトでは冒頭の5行と末尾の5行が表示されるが、途中の行は「・・・」として省略される。`display.max_rows`を200に設定すれば、省略されずに全行が出力される。

```python
dir(pd.options.display)
```

ちなみに、`display.max_rows`以外にも背って項目は存在し、上記のコマンドを実行することで項目の一覧を取得できる。

### reference

1. [pandasで表示が省略されるのを防ぐ](https://uyamazak.hatenablog.com/entry/2016/09/29/163534)

## インスタンスメソッドとクラスメソッドとスタティックメソッドの違い

### detail

Pythonのメソッドには3種類存在する。

- インスタンスメソッド：インスタンスに紐づくメソッド。第1引数は必須で`self`とする。呼び出したとき、第1引数にインスタンスが自動的に設定される。
- クラスメソッド：クラスに紐づくメソッド。第1引数は必須で`cls`とする。呼び出したとき、第1引数にクラスが自動的に設定される。
- スタティックメソッド：クラスに紐づくメソッド。第1引数は**任意**でなくてもよい。インスタンスメソッドやクラスメソッドのように、第1引数に自動的にインスタンスやクラスが設定されるようなことはない。

このうち、使用頻度が一番高いのがインスタンスメソッド。クラスメソッドは、ライブラリに実装されたものを利用することはままあると思う。が、自分で作成したクラスにクラスメソッドとして実装するなら、その機能を関数として実装してしまった方が可読性の面から楽なので、わざわざクラスメソッドを利用するケースはあまり多くない。スタティックメソッドは関数でいいのでは・・・？

```python
>>> class Hoge:
...   foo = 99
...   def __init__(self):
...     self.foo = 1
...   def instance_method(self, arg):
...     return self.foo + arg
...   @classmethod
...   def class_method(cls, arg):
...     return cls.foo + arg
...   @staticmethod
...   def static_method(arg):
...     return Hoge.foo + (arg * 2)
... 
>>> hoge = Hoge()
>>> hoge.instance_method(1)
2
>>> hoge.class_method(1)
100
>>> hoge.static_method(1)
101
>>> Hoge.instance_method(1) # インスタンスメソッドはインスタンスを指定しないとクラス経由で呼び出せない
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: instance_method() missing 1 required positional argument: 'arg'
>>> Hoge.class_method(1)
100
>>> Hoge.static_method(1)
101
>>> Hoge.instance_method(hoge, 1) # クラス経由で呼び出す場合、インスタンスを引数に指定すればインスタンスメソッドを呼び出せる
2
>>> Hoge.instance_method(Hoge, 1) # クラス経由で呼び出す場合、クラスを引数に指定してもインスタンスメソッドを呼び出せる
100
>>> Hoge.class_method(hoge, 1) # クラスメソッドはインスタンスメソッドと異なり、インスタンスやクラスを引数に指定できない
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: class_method() takes 2 positional arguments but 3 were given
>>> Hoge.class_method(Hoge, 1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: class_method() takes 2 positional arguments but 3 were given
>>> Hoge.static_method(hoge, 1) # スタティックメソッドも、インスタンスやクラスを引数に指定できない
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: static_method() takes 1 positional argument but 2 were given
>>> Hoge.static_method(Hoge, 1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: static_method() takes 1 positional argument but 2 were given
>>> 
```

|名前|引数|呼び出し|備考|
|:-:|:-:|:-:|:-:|
|インスタンスメソッド|self|インスタンス経由の場合、呼び出したインスタンスが暗黙的に第1引数に設定される。クラス経由の場合、引数にインスタンスかクラスの明示的な指定が必要。||
|クラスメソッド|cls|インスタンス経由の場合、呼び出したインスタンスの**クラスが暗黙的に**第1引数に設定される。クラス経由の場合、呼び出したクラスが第1引数に暗黙的に設定される。|宣言時に`@classmethod`の記述が必要。|
|スタティックメソッド|なし|呼び出した際に指定した引数が設定される。第1引数にインスタンスやクラスを暗黙的あるいは明示的に指定することはできない。|宣言時に`@staticmethod`の記述が必要。|

先に記述したとおり、基本的にはインスタンスメソッドを使って、クラスメソッドを利用するくらいなら関数を宣言してしまった方がよい。これは、クラスメソッドを利用するより関数を利用するほうが、コード上シンプルになるからだ。可読性は保守性に結び付くので、複雑よりシンプルにすべきだしインデントは1段でも浅い方がいい。クラスメソッドを使うのなら、まずは関数で実装ができないかを検討して、どうしてもクラスメソッドでなければならない、メソッドをそのクラスに紐づけなければならない場合に実装を検討する。ただ、そんなシチュエーションがどれだけあるか・・・。

### reference

1. [Pythonで、呼び出し方によってメソッドの振る舞いを変える](https://qiita.com/masaru/items/5ebf2e96d6524830511b)
2. [Pythonのクラスメソッド（@classmethod）とは？使いどころとメソッドとの違いを解説](https://blog.pyq.jp/entry/Python_kaiketsu_190205)
3. [【Python】インスタンスメソッド、staticmethod、classmethodの違いと使い方](https://djangobrothers.com/blogs/class_instance_staticmethod_classmethod_difference/)

## DataFrameやSeriesでheadとtailを同時に出力する

### detail

pandasのDataFrameやSeriesを出力する際、単純に出力するのではなく冒頭の数行あるいは末尾の数行だけを出力して内容を確認することがある。冒頭であれば`head()`、末尾であれば`tail()`を使う。デフォルトではそれぞれ5行を出力するが、引数に出力する行数を指定できる。

```python
df.head(10)
```

上記の場合、`df`というDataFrameの冒頭10行を表示する。

ただ、場合によっては`head()`と`tail()`を一度に実行して、データの冒頭と末尾を同時に参照したケースがある。これはどう実行すればいいか。

```python
def get_head_and_tail(df, rows=5):
    return df.head(rows).append(df.tail(rows))
```

上記のような関数を作って、実行すればいい。

```python
>>> import pandas as pd
>>> import io
>>> hoge = [1, 2, 3, 4, 5]
>>> s = pd.Series(hoge)
>>> s
0    1
1    2
2    3
3    4
4    5
dtype: int64
>>> def get_head_and_tail(df, rows=5):
...   return df.head(rows).append(df.tail(rows))
... 
>>> get_head_and_tail(s, 1)
0    1
4    5
dtype: int64
>>> get_head_and_tail(s, 2)
0    1
1    2
3    4
4    5
dtype: int64
>>> get_head_and_tail(s)
0    1
1    2
2    3
3    4
4    5
0    1
1    2
2    3
3    4
4    5
dtype: int64
>>> 
```

引数の`df`には、参照したい対象のDataFrameやSeriesを記述する。引数の`rows`は、指定なしだと冒頭および末尾の5行を、指定された場合はその値分の行数を出力する。

```python
>>> s.iloc[0:2].append(s.iloc[3:5])
0    1
1    2
3    4
4    5
dtype: int64
```

ちなみに、`head()`や`tail()`を使わなくても、`iloc`を使うことで似たような出力を得ることはできる。結果は変わらないが、出力行の指定が若干面倒。その点、`head()`や`tail()`は引数に出力したい行数を指定すればいいので直感的。どっちを使うかはお好みで。

### reference

1. [pandas.DataFrame.head](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.head.html)
2. [pandas.DataFrame.tail](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.tail.html)
3. [pandas.DataFrame.iloc](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.iloc.html)
4. [python pandas select both head and tail](https://stackoverflow.com/questions/42504984/python-pandas-select-both-head-and-tail/47958056#47958056)
5. [pandas DataFrameの出力行を絞るheadとtailを同時に使う](https://qiita.com/u1and0/items/00e1a723da490cd25eb4)

## リストのソート方法まとめ

### detail

Pythonでリストをソートする書き方を、DataFrameのソートと混同するのでメモ。

```python
>>> hoge = ['foo', 'bar', 'baz']
>>> sorted(hoge)
['bar', 'baz', 'foo']
>>> hoge
['foo', 'bar', 'baz']
>>> hoge.sort()
>>> hoge
['bar', 'baz', 'foo']
```

- Pythonでリストをソートする場合、`sorted()`と`sort()`の2通りがある。
- 違いは新しいリストを返す（`sorted()`）か、もとのリストを変える（`sort()`）かという点。どちらがいいかは、ケースバイケースだと思う。とりあえず`sorted()`を覚えておけばタプルや文字列にも使える（ただし、リストを返すので変換が必要）から、潰しがきくこっちだろうか。

```python
>>> fuga = 'hogefugapiyo'
>>> sorted(fuga)
['a', 'e', 'f', 'g', 'g', 'h', 'i', 'o', 'o', 'p', 'u', 'y']
>>> ''.join(sorted(fuga))
'aefgghioopuy'
>>> piyo = ('foo', 'bar', 'baz')
>>> sorted(piyo)
['bar', 'baz', 'foo']
>>> tuple(sorted(piyo))
('bar', 'baz', 'foo')
>>> sorted(piyo, reverse=True)
['foo', 'baz', 'bar']
>>> tuple(sorted(piyo, reverse=True))
('foo', 'baz', 'bar')
```

- 文字列に対し`sorted()`を実行すると、リストで結果が返ってくるので`join()`を使って連結する。この際、空文字`''`を使って連結すれば単純な文字列に変換できるし、カンマやハイフンなどで連結することも可能。
- タプルに対し`sorted()`を実行すると、こちらもリストで結果が返ってくるので、`tuple()`を使ってタプルに変換する。
- なお、`sorted()`の引数に`reverse=True`を指定すると降順になる。

ちなみに、DataFrameやSeriesをソートするのは`sort_values()`。`sort_index()`は行や列のインデックスでソートする。

### reference

1. [Pythonでリストをソートするsortとsortedの違い](https://note.nkmk.me/python-list-sort-sorted/)
1. [pandas.DataFrame, Seriesをソートするsort_values, sort_index](https://note.nkmk.me/python-pandas-sort-values-sort-index/)

## Pythonでランダムな文字列を生成する方法

### detail

### reference

必要があったので、Pythonでランダムな文字列を生成するプログラムを作った。ホントにただのランダムな文字列。

```python
import random, string

def get_random_string(length=8, uppercase=True, lowercase=True, digits=True):
    seed_string = ''

    if uppercase:
        seed_string += string.ascii_uppercase
    if lowercase:
        seed_string += string.ascii_lowercase
    if digits:
        seed_string += string.digits
    
    if len(seed_string) > 0:
        result = ''.join([random.choice(seed_string) for i in range(length)])
    else:
        result = ''

    return result
```

内容は簡単で、引数により指定された内容で文字列を一度生成して、その文字列から指定された回数分1文字ランダムで抽出する。

```python
>>> import string
>>> string.ascii_lowercase
'abcdefghijklmnopqrstuvwxyz'
>>> string.ascii_uppercase
'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
>>> string.digits
'0123456789'
```

`string`で始まる部分は、**文字列定数**と呼ばれている定数で、それぞれ大文字や小文字が格納されている。これらの定数を連結して抽出元の文字列を生成している。

```python
>>> get_random_string()
'2TsENwLT'
>>> get_random_string(length=100, uppercase=True, lowercase=False, digits=True)
'0LMQ5Z28CHRQUGYMLHVVQX4VJZFZZXR4OBO13AZNCN6RQ4CTLHGVOS26UH6BKSF5EQ1ZJC6MQDQO53R92JEXSQNLWVMWCUELXQ66'
```

- 実行するときは、引数を一切指定しなければ大文字小文字と数字で8文字分生成する。
- 大文字や小文字、数字の出力を抑制したい場合は`False`を指定すればいい。文字列長はデフォルトでは8文字。
- すべての文字を出力対象にしない場合は、文字列長の長さにかかわらず空文字を返す。

### reference

1. [pythonでランダム文字列の生成](https://hacknote.jp/archives/52068/)
1. [string --- 一般的な文字列操作](https://docs.python.org/ja/3/library/string.html)

## リスト内包表記の記述方法についてまとめ

### detail

```python
[変数に行う処理 for 変数 in リストなどのイテレータ]
```

リスト内包表記は、何かしらの処理を実行しつつ新しいリストを生成した場合に用いる。

```python
>>> [i * 3 for i in range(1, 5)]
[3, 6, 9, 12]
>>> hoge = []
>>> for i in range(1, 5):
...   hoge.append(i * 3)
... 
>>> hoge
[3, 6, 9, 12]
```

例として、3の倍数でリストを作成する方法を考えると上記のように記述できる。ループのもとになるイテレータは既存のものでもいいし、`range()`で生成したものでもいい。`append()`を利用して要素を追加する方法より、記述量が少なくて可読性がよい。表記方法も、角かっこで囲ってあって「リストが出力される」と直感的に理解しやすい。ループ部分もまんま`for`文なので、こちらも理解しやすい。

```python
>>> hoge = 'foobarbaz'
>>> hoge.count('o')
2
>>> [hoge.count(s) for s in ['o', 'ba', 'oo']]
[2, 2, 1]
```

上記の例は、ある文字列において含まれているであろう文字をリストにして、それぞれがいくつ含まれているか個数をリストで返すコード。`string.count()`は引数に指定された文字や文字列が、元の文字列の中で何回出現するかをカウントするメソッド。

```python
[変数に行う処理 for 変数 in イテレータ if 条件式]
```

処理を行う要素について任意の条件を付与したい場合は、上記のような`if`を付与したリスト内包表記を用いる。

```python
>>> [i ** 2 for i in range(10) if i % 2 == 0]
[0, 4, 16, 36, 64]
```

例として、偶数のみ2乗するリスト内包表記を書いてみた。要素のうち、2の余剰がゼロになる（偶数）という条件が成立する要素のみ対象とする、という条件を`if`部分に記述することで、処理対象の要素を限定できる。

### reference

1. [Pythonリスト内包表記の使い方](https://note.nkmk.me/python-list-comprehension/)
1. [リスト内包表記の活用と悪用](https://qiita.com/KTakahiro1729/items/c9cb757473de50652374)

## Pythonでpandas.DataFrameに対し統計量を取得する`describe()`を実行するときの注意点について

### detail

ゼロを含んだDataFrameで`describe()`を実行したい場合、**ケースによっては**一度ゼロを除外する方がいい。ゼロが意味を持つデータならいいが、意味を持たない（欠損値と変わらない）ようなデータの場合、ゼロを含んだ状態で統計量を出すと、本来の意味とは異なる統計量が出てきてしまう。とくに最小値が必ずゼロになってしまうため、標準偏差もゼロを含んだ場合と含まない場合で差が出てくる。

```python
df.describe()
df[df > 0.0].describe()
```

`df`がゼロを含むデータであった場合、1行目と2行目の結果は異なる。`df[df > 0.0]`の場合、ゼロはデータとして抽出されないためNaNになる。`describe()`は**NaNを無視する**ため、2行目は1行目のゼロを含んだ結果と異なる結果を出力する。

### reference

1. [pandasのdescribeで各列の要約統計量（平均、標準偏差など）を取得](https://note.nkmk.me/python-pandas-describe/)

## Pythonでラムダ式に複数の引数を設定する方法について。

### detail

ラムダ式に複数の引数を設定する場合、単純に「引数設定の際に複数の引数を記述」すればいい。`def`による関数宣言を同様に、引数の初期値設定が可能。

```python
>>> hoge = lambda a, b=2: a * b
>>> hoge(1)
2
>>> hoge(1, 3)
3
>>> hoge()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: <lambda>() missing 1 required positional argument: 'a'
>>> hoge(b=100, a=4)
400
>>> 
```

上記の場合、引数bに2が初期設定されているため、引数aだけ指定してやれば実行可能。引数が指定されなければエラーになるのも、`def`による関数宣言と同じ。

### reference

1. [Pythonのlambda（ラムダ式、無名関数）の使い方](https://note.nkmk.me/python-lambda-usage/)

## pandasのDataFrameで既存列の値を利用して新しい列を新規追加する方法

### detail

DataFrameの既存列に格納されている値を使用して、何らかの計算を行って結果を新しい列に格納したい。その場合は、`DataFrame.apply()`を使ってラムダ式を`axis=1`のオプションとともに実行することで実現できる。

```python
>>> import pandas as pd
>>> import numpy as np
>>> import random
>>> input_data = np.arange(30)
>>> input_data = map(lambda x: x * round(random.random(), 1), input_data)
>>> input_data = list(input_data)
>>> len(input_data)
30
>>> df = pd.DataFrame(np.array(input_data).reshape(10, 3), columns=['foo', 'bar', 'baz'])
>>> df.head()
   foo  bar  baz
0  0.0  0.9  1.6
1  2.1  3.6  2.0
2  5.4  0.0  0.8
3  2.7  9.0  6.6
4  1.2  3.9  9.8
>>> df['hoge'] = df.apply(lambda x: x['foo'] + x['bar'] + x['baz'], axis=1)
>>> df.head()
   foo  bar  baz  hoge
0  0.0  0.9  1.6   2.5
1  2.1  3.6  2.0   7.7
2  5.4  0.0  0.8   6.2
3  2.7  9.0  6.6  18.3
4  1.2  3.9  9.8  14.9
```

テストデータは、numpyとrandomを使って任意の値を用意する。

`df.apply(lambda x: x['foo'] + x['bar'] + x['baz'], axis=1)`とすることで、既存列（`axis=1`のオプションで、処理対象が行であると指定している。指定しないか、`axis=0`の場合は列が対象になる）の値を使って、ラムダ式に記述した処理を**全行に対し**行う、という意味になる。つまり、ラムダ式の引数xには**各行が1つずつ入力**され、各行の項目列である**fooとbarとbazの値を加算しろ**、という処理になる。この結果はSeries形式で返ってくるので、任意の名前を付けて新規列として既存のDataFrameに追加が可能。

```python
>>> df.apply(lambda x: max(x))
foo     27.0
bar     17.6
baz     20.8
hoge    47.7
dtype: float64
```

`axis=1`を指定しない場合は、処理の対象が列になる。つまり、ラムダ式の引数xに**各列が1つずつ入力**される。ここの挙動が上記と異なるところ。上記では`max()`を使って、各列における最大値を取得している。処理対象を列であるため、各列における最大値が出力されている。

### reference

1. [pandas.DataFrame.apply](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.apply.html)
1. [Pandasで複数の列を値をもとに、新しい列を任意の関数で定義する方法](https://blog.shikoan.com/pandas-newcolumn-lambda/)

## matplotlibで描画したグラフに、補助線を追加する方法

### detail

matplotlibで描画したグラフに、説明のためなどで補助線を描画したい場合は、水平線なら`hlines()`、垂直線なら`vlines()`を利用する。

```python
sns.lineplot(x=df.index, y=sensor_df['saturation'])
plt.hlines(y=3, xmin=df.index[0], xmax=df.index[-1], linestyles='--', colors='red')
plt.hlines(y=6, xmin=df.index[0], xmax=df.index[-1], linestyles='--', colors='red')
plt.show()
```

もちろん、複数の線を出力することも可能。単純に描画したい数だけ、コードを書いてやればいい。

### reference

1. [Matplotlib で水平線と垂直線をプロットする方法](https://www.delftstack.com/ja/howto/matplotlib/how-to-plot-horizontal-and-vertical-line-in-matplotlib/)

## matplotlibで描画したグラフのX軸とY軸の幅を調査する方法

### detail

matplotlibでグラフを描画した際、グラフのX軸とY軸がそれぞれどのくらいのレンジ（幅）で描画されているかを調べたい場合は、`xlim()`と`ylim()`を実行する。

```python
sns.lineplot(x=df.index, y=df['hoge'])
print(plt.xlim())
print(plt.ylim())
plt.show()
```

```console
(18107.05, 18479.95)
(1.0695084678691622, 12.550942168141958)
```

グラフを描画する際、とくに指定しなければmatplotlibが自動で、指定されたデータの最大値および最小値が十分に収まるような、適当な幅をX軸とY軸に設定しグラフを出力する。この際、X軸とY軸の出力幅を変えたければ`xlim(x軸の最小値, x軸の最大値)`や`ylim(y軸の最小値, y軸の最大値)`といった指定を行う。

ところが、日付などを出力の軸にしていえる場合、その最大値や最小値の具体的な数値がわからない場合がある。データ上の最大値や最小値を参照してもよいが、たとえば「出力したグラフに補助線を引きたい」という場合、補助線を引きたい座標の**具体的な数値**を指定しなければならない。日付はあくまでも**見た目のラベルであって座標ではない**ので、日付指定による補助線の座標指定はできない。その場合、描画されているグラフのX軸とY軸の最大値と最小値の具体的な数値を取得し、その数字に則って座標を決めることで補助線の座標を指定できる。

```python
import matplotlib.patches as patches

sns.lineplot(x=df.index, y=df['hoge'])
e = patches.Rectangle(xy=(18105, 3) , width=18470, height=3, ec='#523245', fill=True, alpha=0.3, color='r')
ax = plt.axes()
ax.add_patch(e)
plt.show()
```

上記では、取得した座標に則って四角を`Rectangle()`関数で描画し、グラフに出力している。

### reference

1. [Matplotlibで円や長方形などの図形を描画](https://note.nkmk.me/python-matplotlib-patches-circle-rectangle/)

## pandasl.DataFrameで差分を取る方法

### detail

DataFrameのデータにおいて、データ間の差分を取得したい場合は`diff()`を使う。

```python
>>> quit()
root@25d8182164be:/workspaces/env_2020# python
Python 3.8.5 (default, Aug  4 2020, 16:24:08) 
[GCC 8.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import pandas as pd
>>> import io
>>> s = '''a,b,c
... 1,2,3
... 1,3,5
... 3,6,9
... 9,8,7
... 10,0,1
... '''
>>> df = pd.read_csv(io.StringIO(s))
>>> df
    a  b  c
0   1  2  3
1   1  3  5
2   3  6  9
3   9  8  7
4  10  0  1
>>> df.diff()
     a    b    c
0  NaN  NaN  NaN
1  0.0  1.0  2.0
2  2.0  3.0  4.0
3  6.0  2.0 -2.0
4  1.0 -8.0 -6.0
```

引数なしでは、行ごとに「1行前のデータと比較して、差分を各項目列ごとに取得」する。結果の1行目は全項目がNaNになっているが、これは「0行目との比較はできない」ため。

```python
>>> df.diff(2)
     a    b    c
0  NaN  NaN  NaN
1  NaN  NaN  NaN
2  2.0  4.0  6.0
3  8.0  5.0  2.0
4  7.0 -6.0 -8.0
>>> df.diff(3)
     a    b    c
0  NaN  NaN  NaN
1  NaN  NaN  NaN
2  NaN  NaN  NaN
3  8.0  6.0  4.0
4  9.0 -3.0 -4.0
>>> df.diff(-2)
     a    b    c
0 -2.0 -4.0 -6.0
1 -8.0 -5.0 -2.0
2 -7.0  6.0  8.0
3  NaN  NaN  NaN
4  NaN  NaN  NaN
>>> df.diff(-1)
     a    b    c
0  0.0 -1.0 -2.0
1 -2.0 -3.0 -4.0
2 -6.0 -2.0  2.0
3 -1.0  8.0  6.0
4  NaN  NaN  NaN
```

引数`periods`を指定すると、何行前のデータと差分を取るか指定できる。`2`であれば、2行前のデータを差分を取る。デフォルトでは1行前と差分を取る。

```python
>>> df.diff(axis=1)
    a     b    c
0 NaN   1.0  1.0
1 NaN   2.0  2.0
2 NaN   3.0  3.0
3 NaN  -1.0 -1.0
4 NaN -10.0  1.0
>>> df.diff(axis=0)
     a    b    c
0  NaN  NaN  NaN
1  0.0  1.0  2.0
2  2.0  3.0  4.0
3  6.0  2.0 -2.0
4  1.0 -8.0 -6.0
```

引数`axis`を指定すると、差分を取る方向を指定できる。デフォルトは0で、行方向に差分を取る。`axis=1`とすると、列方向に差分を取る。

変化率を取得したい場合は`pct_change()`を使う。

```python
>>> df.pct_change()
          a         b         c
0       NaN       NaN       NaN
1  0.000000  0.500000  0.666667
2  2.000000  1.000000  0.800000
3  2.000000  0.333333 -0.222222
4  0.111111 -1.000000 -0.857143
```

`diff()`とは異なるのは出力が差分ではなく変化率である点だけで、使い方は`diff()`と一緒。

```python
>>> df.pct_change(2)
          a         b         c
0       NaN       NaN       NaN
1       NaN       NaN       NaN
2  2.000000  2.000000  2.000000
3  8.000000  1.666667  0.400000
4  2.333333 -1.000000 -0.888889
>>> df.pct_change(-1)
          a         b         c
0  0.000000 -0.333333 -0.400000
1 -0.666667 -0.500000 -0.444444
2 -0.666667 -0.250000  0.285714
3 -0.100000       inf  6.000000
4       NaN       NaN       NaN
```

引数`periods`の設定方法も一緒。マイナスが利用できるところも同じ。

```python
>>> df.pct_change(axis=1)
    a         b         c
0 NaN  1.000000  0.500000
1 NaN  2.000000  0.666667
2 NaN  1.000000  0.500000
3 NaN -0.111111 -0.125000
4 NaN -1.000000       inf
>>> df.pct_change(axis=0)
          a         b         c
0       NaN       NaN       NaN
1  0.000000  0.500000  0.666667
2  2.000000  1.000000  0.800000
3  2.000000  0.333333 -0.222222
4  0.111111 -1.000000 -0.857143
```

引数`axis`の設定方法も一緒。

### reference

1. [pandasで行・列の差分・変化率を取得するdiff, pct_change](https://note.nkmk.me/python-pandas-diff-pct-change/)
