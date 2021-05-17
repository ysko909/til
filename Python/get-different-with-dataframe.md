# pandasl.DataFrameで差分を取る方法

## detail

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

## reference

1. [pandasで行・列の差分・変化率を取得するdiff, pct_change](https://note.nkmk.me/python-pandas-diff-pct-change/)
