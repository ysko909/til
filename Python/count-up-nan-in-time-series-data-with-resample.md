# `resample`を使って時系列データに含まれる欠損値をカウントする

## detail

センサーなどを使って任意の単位時間あたりでデータを取得するとき、取得された結果は時系列データとなる。このとき、何らかの理由でデータが取得できないと、その部分は欠損値となる。今回は時系列データに含まれている欠損値について、「月ごとに集計すると各月でどのくらい欠損しているか」を確認できるような方法について記述する。

```python
>>> import pandas as pd
impo>>> import numpy as np
>>> data = list(np.random.randint(0, 100, (200))) + [np.nan] * 40
>>> data = np.random.permutation(data)
>>> data = data.reshape(48, 5)
>>> col_name = ['col{:02}'.format(i) for i in range(data.shape[1])]
>>> idx_name = pd.date_range('2020/1/1', '2021/1/1', freq='W')
>>> df = pd.DataFrame(data=data, index=idx_name[:data.shape[0]], columns=col_name)
>>> print(df)
            col00  col01  col02  col03  col04
2020-01-05   19.0    NaN   48.0   49.0   29.0
2020-01-12    7.0   42.0   88.0   21.0   83.0
2020-01-19   89.0   29.0   33.0   67.0   99.0
2020-01-26   37.0   38.0   84.0   45.0    7.0
2020-02-02   85.0    NaN   29.0   73.0   66.0
2020-02-09   35.0   71.0    NaN   88.0   84.0
2020-02-16   99.0   91.0   43.0    NaN   54.0
2020-02-23    1.0    NaN    NaN   99.0   20.0
2020-03-01   89.0   66.0   59.0    2.0   59.0
2020-03-08   36.0    6.0    NaN   18.0    NaN
2020-03-15   31.0   29.0    6.0    NaN   30.0
2020-03-22   80.0    NaN   84.0   78.0   32.0
2020-03-29   51.0   22.0    7.0    0.0   74.0
2020-04-05    NaN   81.0   99.0   28.0    NaN
2020-04-12   19.0   71.0   89.0    NaN   41.0
2020-04-19   25.0    NaN   15.0    NaN   18.0
2020-04-26   85.0   81.0   96.0    NaN   63.0
2020-05-03    NaN   26.0   14.0    NaN   78.0
2020-05-10   79.0    4.0   58.0   50.0   92.0
2020-05-17   55.0   91.0   83.0   66.0    NaN
2020-05-24   29.0   81.0   70.0    NaN   17.0
2020-05-31   22.0    NaN   43.0   39.0    4.0
2020-06-07    1.0    2.0   25.0   49.0   46.0
2020-06-14   13.0   72.0   95.0   82.0   78.0
2020-06-21   52.0   42.0    NaN    NaN   79.0
2020-06-28    NaN   22.0   85.0   26.0   74.0
2020-07-05   45.0   22.0   29.0   21.0   50.0
2020-07-12    4.0    NaN   58.0    NaN   33.0
2020-07-19   75.0   91.0    8.0   84.0    NaN
2020-07-26   67.0   94.0   98.0    NaN   17.0
2020-08-02   58.0   42.0   81.0   41.0   38.0
2020-08-09   53.0   59.0   69.0    NaN   58.0
2020-08-16   99.0   85.0   89.0   14.0   91.0
2020-08-23   34.0   66.0   88.0   22.0   60.0
2020-08-30    NaN   57.0    NaN    0.0   37.0
2020-09-06   32.0   25.0   20.0   49.0   68.0
2020-09-13   78.0    NaN    NaN    6.0   40.0
2020-09-20   93.0   52.0   49.0    NaN   45.0
2020-09-27    5.0   45.0   95.0   79.0    0.0
2020-10-04   21.0   70.0   53.0   76.0    NaN
2020-10-11   57.0   80.0   43.0   33.0   28.0
2020-10-18    3.0   47.0    NaN    NaN   63.0
2020-10-25   56.0   80.0    3.0   77.0    NaN
2020-11-01   40.0   19.0    0.0   80.0    0.0
2020-11-08    3.0   52.0   94.0   20.0   89.0
2020-11-15    NaN   11.0   73.0   16.0    NaN
2020-11-22   39.0   10.0   71.0   90.0   34.0
2020-11-29   52.0   77.0   42.0    3.0   31.0
>>> resample_df = df.resample(rule='M')
>>> type(resample_df)
<class 'pandas.core.resample.DatetimeIndexResampler'>
>>> print(resample_df.sum())
            col00  col01  col02  col03  col04
2020-01-31  152.0  109.0  253.0  182.0  218.0
2020-02-29  220.0  162.0   72.0  260.0  224.0
2020-03-31  287.0  123.0  156.0   98.0  195.0
2020-04-30  129.0  233.0  299.0   28.0  122.0
2020-05-31  185.0  202.0  268.0  155.0  191.0
2020-06-30   66.0  138.0  205.0  157.0  277.0
2020-07-31  191.0  207.0  193.0  105.0  100.0
2020-08-31  244.0  309.0  327.0   77.0  284.0
2020-09-30  208.0  122.0  164.0  134.0  153.0
2020-10-31  137.0  277.0   99.0  186.0   91.0
2020-11-30  134.0  169.0  280.0  209.0  154.0
>>> count_nan_df = df.isnull().resample(rule='M').sum()
>>> print(count_nan_df)
            col00  col01  col02  col03  col04
2020-01-31      0      1      0      0      0
2020-02-29      0      2      2      1      0
2020-03-31      0      1      1      1      1
2020-04-30      1      1      0      3      1
2020-05-31      1      1      0      2      1
2020-06-30      1      0      1      1      0
2020-07-31      0      1      0      2      1
2020-08-31      1      0      1      1      0
2020-09-30      0      1      1      1      0
2020-10-31      0      0      1      1      2
2020-11-30      1      0      0      0      1
>>> 
```

時系列データについて、もともとのデータをある単位時間で集約した場合は`resample()`を使う。引数の`freq`で集約したい単位を指定するが、上記では`'M'`を指定して、月単位での集計を行っている。これを`'Q'`とすると、四半期ごとの集計となる。

`isnull()`で出力した結果はTrueかFalseしか含まれていないデータフレームだが、これをリサンプルして`sum()`することでTrueを1としてカウントできる。そのため、各集計単位で欠損値をカウントすることになる。

```python
>>> count_nan_df_Q = df.isnull().resample(rule='Q').sum()
>>> print(count_nan_df_Q)
            col00  col01  col02  col03  col04
2020-03-31      0      4      3      2      1
2020-06-30      3      2      1      6      2
2020-09-30      1      2      2      4      1
2020-12-31      1      0      1      1      3
```

リサンプルの単位を`'Q'`にして実行してみた結果。

## reference

1. [pandasで時系列データをリサンプリングするresample, asfreq](https://note.nkmk.me/python-pandas-time-series-resample-asfreq/)
2. [pandasの時系列データにおける頻度（引数freq）の指定方法](https://note.nkmk.me/python-pandas-time-series-freq/)