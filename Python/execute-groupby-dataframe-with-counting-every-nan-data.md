# 欠損値を含む行がどのくらい連続しているかをカウントして結果を出力する

## detail

欠損値を含むデータを扱っているとき、その欠損値が「どのくらいの間、連続して欠損しているか」を調査したい。このとき、連続欠損期間が連続でどれだけあるかを出力する方法についてメモする。

なお、ここで言う「連続」とは入力データの行において、欠損値を含む行が連続して存在していることを表す。よって、日次のデータであれば連続n日となるし、時間単位でのデータであれが連続m時間となるように、単位はデータの取得スパンに依存している。

```python
def count_nan_data_rows(df, *, col=[], nan_rows=3):
    # 連続欠損期間を出力する

    if len(col) == 0:
        input_temp_df = df.copy()

    else:
        input_temp_df = df[col].copy()

    input_temp_df['nan_flag'] = input_temp_df.iloc[:, :].isnull().any(axis=1)

    input_temp_df['count_rows'] = input_temp_df.groupby((input_temp_df['nan_flag'] != input_temp_df['nan_flag'].shift()).cumsum())['nan_flag'].transform(sum)
    input_temp_df['ge_7d'] = input_temp_df['count_rows'] >= 7
    env_count_nan_rows = input_temp_df.groupby((input_temp_df['nan_flag'] != input_temp_df['nan_flag'].shift()).cumsum())['nan_flag'].agg(
        start=lambda x: x.index[0],
        end=lambda x: x.index[-1],
        rows=sum
    )

    env_count_nan_rows = env_count_nan_rows[env_count_nan_rows['rows'] >= nan_rows]

    return env_count_nan_rows
```

こんな関数を用意した。引数に欠損値を含むDataFrame、欠損値の有無を確認したい項目列のリスト、何行欠損値が連続しているかを指定する。DataFrame以外は、オプション指定とした。引数にDataFrameだけ指定した場合は、いずれかの項目列に欠損値を含んでいたらTrueとして評価し、3行以上連続してTrueの評価となった行について開始行と終了行のインデックス、およびどれだけ連続しているかの行数を出力する。

```python
import numpy as np
import pandas as pd


def count_nan_data_rows(df, *, col=[], nan_rows=3):
    # 連続欠損期間を出力する

    if len(col) == 0:
        input_temp_df = df.copy()

    else:
        input_temp_df = df[col].copy()

    input_temp_df['nan_flag'] = input_temp_df.isnull().any(axis=1)

    input_temp_df['count_rows'] = input_temp_df.groupby((input_temp_df['nan_flag'] != input_temp_df['nan_flag'].shift()).cumsum())['nan_flag'].transform(sum)
    input_temp_df['ge_7d'] = input_temp_df['count_rows'] >= 7
    env_count_nan_rows = input_temp_df.groupby((input_temp_df['nan_flag'] != input_temp_df['nan_flag'].shift()).cumsum())['nan_flag'].agg(
        start=lambda x: x.index[0],
        end=lambda x: x.index[-1],
        rows=sum
    )

    env_count_nan_rows = env_count_nan_rows[env_count_nan_rows['rows'] >= nan_rows]

    return env_count_nan_rows


data = list(np.random.randint(0, 100, (200))) + [np.nan] * 40
data = np.random.permutation(data)

data = data.reshape(24, 10)

idx = ['index{:02}'.format(i) for i in range(data.shape[0])]
col = ['column{:02}'.format(i) for i in range(data.shape[1])]

df = pd.DataFrame(data=data, index=idx, columns=col)

print(df)
print(count_nan_data_rows(df))
print(count_nan_data_rows(df, col=['column03'], nan_rows=2))
print(count_nan_data_rows(df, col=['column00', 'column02', 'column05', 'column09'], nan_rows=4))

```

欠損値を含みつつランダムに作ったデータをDataFrame化して、さっきの関数に入れてみる。

```console
         column00  column01  column02  column03  column04  column05  column06  column07  column08  column09
index00      20.0       NaN      58.0      91.0       NaN       NaN       NaN      53.0       3.0       NaN
index01      92.0      67.0      96.0      90.0      11.0       NaN       NaN      72.0      14.0      50.0
index02      20.0      10.0      57.0      48.0      73.0       7.0      51.0       0.0      35.0      35.0
index03      35.0      94.0      67.0      27.0      30.0       NaN      96.0      62.0       8.0      35.0
index04      54.0      90.0       NaN      82.0      56.0       NaN      78.0      30.0      53.0      67.0
index05      44.0      10.0      67.0      87.0       NaN      76.0       NaN      25.0      65.0      33.0
index06       1.0       7.0       3.0       NaN       NaN       5.0      15.0      79.0       9.0      42.0
index07      44.0      79.0      17.0       NaN      98.0      76.0      25.0      11.0      39.0      92.0
index08       NaN       NaN      56.0      77.0      42.0      74.0      11.0      26.0      85.0      91.0
index09       5.0      32.0       NaN      83.0      62.0      96.0      83.0      20.0      61.0      13.0
index10      99.0      21.0      51.0       NaN      48.0       NaN      14.0      27.0      35.0      47.0
index11      42.0       1.0      79.0      59.0       NaN      68.0      41.0      87.0       1.0       NaN
index12      51.0      91.0       NaN      99.0       NaN      79.0      29.0      70.0      36.0      80.0
index13      14.0      26.0      14.0       5.0      62.0      32.0      63.0      68.0      23.0      96.0
index14      21.0      39.0      67.0      57.0      82.0      72.0      12.0      65.0       0.0      44.0
index15      11.0      81.0       NaN       NaN      72.0      95.0      48.0      88.0      91.0      79.0
index16      95.0      14.0      72.0       NaN      47.0       NaN      47.0      11.0      77.0      72.0
index17       7.0      29.0      61.0      65.0      69.0      41.0       NaN      83.0      21.0      98.0
index18       NaN       2.0      67.0      37.0       NaN      73.0      70.0      55.0      12.0      75.0
index19      14.0      48.0      96.0      95.0       7.0      57.0      27.0      91.0      11.0       NaN
index20      69.0       NaN      50.0      57.0       NaN       NaN      39.0      51.0      79.0      86.0
index21      13.0      75.0      63.0      11.0      56.0       NaN      22.0      54.0      26.0       NaN
index22      53.0      19.0      91.0      55.0      62.0      91.0      43.0      84.0      54.0      92.0
index23       NaN      45.0      16.0      29.0      26.0       NaN      76.0       NaN      32.0      49.0
            start      end  rows
nan_flag                        
3         index03  index12    10
5         index15  index21     7
            start      end  rows
nan_flag                        
2         index06  index07     2
6         index15  index16     2
            start      end  rows
nan_flag                        
5         index08  index12     5
9         index18  index21     4
```

結果はこんな感じ。

```python
print(count_nan_data_rows(df))
```

単純にDataFrameだけ引数に指定すると、全項目を対象に欠損値の有無を**OR条件**調査する。つまり、どれか1つの項目列に欠損値があれば、その行は「欠損値がある」と評価される。そのうえで、評価結果が真である、つまり**いずれかの項目列に欠損値を含む**行をカウントする。そのうえで、欠損値を含む行が**3行**以上連続して存在している場合に開始行と終了行のインデックス名、それから欠損値が欠落している行数を出力する。

インデックス「index03」から「index12」までは、いずれかの項目列に欠損値を含んでおり、連続で10行が欠損値を含む行であるとカウントされている。「index15」から「index21」も同様だ。

インデックス「index23」は欠損値を含んでいるものの、直前の行が欠損値を含んでいないため「欠損値を含む行」が連続しておらず、出力対象外となっている。

```python
print(count_nan_data_rows(df, col=['column03'], nan_rows=2))
```

引数`col`には、欠損値の有無を調査したい対象の列を指定する。引数`nan_rows`に数値を指定すると、指定された数値以上に連続して欠損値を含む行が存在していれば、出力対象となる。今回は「column03」という項目列において、連続2行以上欠損値を含む行を出力対象としている。

```python
print(count_nan_data_rows(df, col=['column00', 'column02', 'column05', 'column09'], nan_rows=4))
```
最後の出力は、調査対象としたい行を複数指定するパターン。

よく考えたら、別に時系列データじゃなくても単純に「ある項目において行で見たときに連続して欠損している」ようなテーブルデータは、上記の方法でカウントができる。まぁ、特定の条件で行数をカウントするのって、大抵は時系列データだったりするので一応間違ってはいないとは思うんだけど。

## reference

1. [[Pandas] 系列データの同じ値が連続する区間毎にgroupbyする方法](http://ynomura.dip.jp/archives/2020/08/pandas-groupby.html)
2. [pandasで任意の位置の値を取得・変更するat, iat, loc, iloc](https://note.nkmk.me/python-pandas-at-iat-loc-iloc/)
3. [pandasで欠損値NaNが含まれているか判定、個数をカウント](https://note.nkmk.me/python-pandas-nan-judge-count/)
4. [pandas の SettingWithCopyWarning で苦労した話](https://qiita.com/HEM_SP/items/56cd62a1c000d342bd70)
5. [Python, formatで書式変換（0埋め、指数表記、16進数など）](https://note.nkmk.me/python-format-zero-hex/)
6. [Pythonで一次元配列を二次元配列に変換（numpy.ndarray、リスト）](https://note.nkmk.me/python-list-ndarray-1d-to-2d/)
7. [pandas.DataFrame, SeriesとNumPy配列ndarrayを相互に変換](https://note.nkmk.me/python-pandas-numpy-conversion/)
8. [pandasで欠損値NaNが含まれているか判定、個数をカウント](https://note.nkmk.me/python-pandas-nan-judge-count/)
