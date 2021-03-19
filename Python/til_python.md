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
3. [python pandas select both head and tail](https://stackoverflow.com/questions/42504984/python-pandas-select-both-head-and-tail/47958056#47958056)
4. [pandas DataFrameの出力行を絞るheadとtailを同時に使う](https://qiita.com/u1and0/items/00e1a723da490cd25eb4)
