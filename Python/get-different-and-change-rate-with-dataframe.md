# DataFrameの行や列で差分・変化率を取得する

## detail

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

## reference

1. [pandas.DataFrame.diff](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.diff.html)
2. [pandas.DataFrame.pct_change](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.pct_change.html)
3. [pandasで行・列の差分・変化率を取得するdiff, pct_change](https://note.nkmk.me/python-pandas-diff-pct-change/)
