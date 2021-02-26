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

**リンク**:

1. [pandasの時系列データにおける頻度（引数freq）の指定方法](https://note.nkmk.me/python-pandas-time-series-freq/)
1. [pandas.DataFrameの構造とその作成方法](https://note.nkmk.me/python-pandas-dataframe-values-columns-index/)
1. [pandas で日時の連続値を生成し、インデックスに使う](http://ailaby.com/date_range/)

