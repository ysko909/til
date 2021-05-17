# DataFrameやSeriesでheadとtailを同時に出力する

## detail

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

## reference

1. [pandas.DataFrame.head](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.head.html)
2. [pandas.DataFrame.tail](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.tail.html)
3. [pandas.DataFrame.iloc](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.iloc.html)
4. [python pandas select both head and tail](https://stackoverflow.com/questions/42504984/python-pandas-select-both-head-and-tail/47958056#47958056)
5. [pandas DataFrameの出力行を絞るheadとtailを同時に使う](https://qiita.com/u1and0/items/00e1a723da490cd25eb4)
