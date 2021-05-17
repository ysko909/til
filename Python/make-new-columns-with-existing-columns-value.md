# pandasのDataFrameで既存列の値を利用して新しい列を新規追加する方法

## detail

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

## reference

1. [Matplotlib で水平線と垂直線をプロットする方法](https://www.delftstack.com/ja/howto/matplotlib/how-to-plot-horizontal-and-vertical-line-in-matplotlib/)
