# pandas.DataFrameにおける欠損値を除去する方法

## detail

DataFrameの欠損値を含む行を削除する場合は`dropna()`を利用する。

```python
>>> import pandas as pd
>>> import io
>>> s = '''a,b,c
... 1,,3
... 4,5,6
... 7,8,
... ,,
... ,14,15
... '''
>>> df = pd.read_csv(io.StringIO(s))
>>> df
     a     b     c
0  1.0   NaN   3.0
1  4.0   5.0   6.0
2  7.0   8.0   NaN
3  NaN   NaN   NaN
4  NaN  14.0  15.0
>>> df.dropna()
     a    b    c
1  4.0  5.0  6.0
```

引数なしで実行した場合は、欠損値を1つでも含む行すべてを削除する。

```python
>>> df.dropna(how='all')
     a     b     c
0  1.0   NaN   3.0
1  4.0   5.0   6.0
2  7.0   8.0   NaN
4  NaN  14.0  15.0
```

引数`how`にallを指定した場合は、項目列すべてが欠損している行を削除する。

```python
>>> df.dropna(how='any')
     a    b    c
1  4.0  5.0  6.0
```

引数`how`に`any`を指定した場合は、欠損値を1つ以上含む行すべてを削除する。引数なしの場合と同じ挙動。

```python
>>> df.dropna(subset=['b'])
     a     b     c
1  4.0   5.0   6.0
2  7.0   8.0   NaN
4  NaN  14.0  15.0
>>> df.dropna(subset=['a', 'b'])
     a    b    c
1  4.0  5.0  6.0
2  7.0  8.0  NaN
>>> df.dropna(subset=['a', 'b'], how='all')
     a     b     c
0  1.0   NaN   3.0
1  4.0   5.0   6.0
2  7.0   8.0   NaN
4  NaN  14.0  15.0
>>> 
```

特定の項目列を対象として欠損値の有無を判断した場合は、引数`subset`に項目名をリスト形式で指定する。この場合、指定された列の**いずれかに欠損値が含まれている**行を削除する。引数`subset`は引数`how`と両立でき、`subset`で項目列を指定しつつ`how`でallを指定すると指定された列の**すべてに欠損値が含まれている**行を削除する。

## reference

1. [pandasで欠損値NaNを除外（削除）・置換（穴埋め）・抽出](https://note.nkmk.me/python-pandas-nan-dropna-fillna/)
