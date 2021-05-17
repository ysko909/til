# DataFrameをインデックスなしで出力する方法

## detail

DataFrameを出力する際、何らかの理由でインデックスの出力が不要である場合は`index=False`というオプションを指定することで、インデックスの出力を抑止できる。

```python
>>> import pandas
>>> import io
>>> s = '''a,b,c
... 1,2,3
... 4,5,6
... 7,8,9
... '''
>>> df = pandas.read_csv(io.StringIO(s))
>>> df
   a  b  c
0  1  2  3
1  4  5  6
2  7  8  9
>>> df.to_csv()
',a,b,c\n0,1,2,3\n1,4,5,6\n2,7,8,9\n'
>>> df.to_csv(index=False)
'a,b,c\n1,2,3\n4,5,6\n7,8,9\n'
>>> df.to_string()
'   a  b  c\n0  1  2  3\n1  4  5  6\n2  7  8  9'
>>> df.to_string(index=False)
' a  b  c\n 1  2  3\n 4  5  6\n 7  8  9'
```

`index=False`をつけると、出力先（CSVか文字列化）に関わらずインデックスの出力が抑制されている。

## reference

1. [How to print pandas DataFrame without index](https://stackoverflow.com/questions/24644656/how-to-print-pandas-dataframe-without-index)
