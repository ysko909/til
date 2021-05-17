# seaborn利用時の文字出力やグラフの描画サイズを調整する方法

## detail

`heatmap()`における`annot`などの文字出力において、フォントのサイズを指定する方法は、`seaborn.set(font_scale=N)`として設定する。

```python
import seaborn as sns

sns.set(font_scale=3)
```

デフォルト設定は1。`font_scale`は、**どのくらい拡大あるいは縮小するか**というスケールの設定なので小数の設定も可能。

グラフの描画サイズ設定は、おなじみの`pyplot.figure(figsize=(X, Y))`で設定する。

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(9, 6))
sns.heatmap(df_diff.corr(), annot=True)
```

この設定方法は`heatmap()`以外にも使える。

### reference

1. [How to make seaborn.heatmap larger (normal size)?](https://stackoverflow.com/questions/41519991/how-to-make-seaborn-heatmap-larger-normal-size)
1. [Auto adjust font size in seaborn heatmap](https://stackoverflow.com/questions/33104322/auto-adjust-font-size-in-seaborn-heatmap) 

## DataFrameの列名にプレフィックス（接頭辞）あるいはサフィックス（接尾辞）を追加する方法

### detail

DataFrameには、列名にプレフィックス（接頭辞）あるいはサフィックス（接尾辞）を追加する機能がある。

- 列名にプレフィックス（接尾辞）を追加する場合は、`prefix()`を利用する
- 列名にサフィックス（接尾辞）を追加する場合は、`suffix()`を利用する

```python
>>> import pandas as pd
>>> import io
>>> s = '''a,b,c
... 1,2,3
... 4,5,6
... 7,8,9
... '''
>>> df = pd.read_csv(io.StringIO(s))
>>> df
   a  b  c
0  1  2  3
1  4  5  6
2  7  8  9
>>> df.add_prefix('hoge')
   hogea  hogeb  hogec
0      1      2      3
1      4      5      6
2      7      8      9
>>> df.add_suffix('foo')
   afoo  bfoo  cfoo
0     1     2     3
1     4     5     6
2     7     8     9
>>> df.add_prefix('fuga_')
   fuga_a  fuga_b  fuga_c
0       1       2       3
1       4       5       6
2       7       8       9
>>> df.add_suffix('_bar')
   a_bar  b_bar  c_bar
0      1      2      3
1      4      5      6
2      7      8      9
```

どちらも指定された文字列をすべての列名に付与する。

```python
>>> df['b'].add_prefix('piyo_')
piyo_0    2
piyo_1    5
piyo_2    8
Name: b, dtype: int64
>>> df['a'].add_suffix('_baz')
0_baz    1
1_baz    4
2_baz    7
Name: a, dtype: int64
```

任意の1列のみ、プレフィックスやサフィックスを追加しようとして上記のように記述するのは**間違い**。結果を見るとわかるが、列名ではなく行名（インデックス）に追加されてしまっている。これは、DataFrameのうち1列のみを選択してしまったため、DataFrameではなく**Seriesに対し処理を行ってしまった**から。

```python
>>> df.rename(columns={'b':'b_bbbb'})
   a  b_bbbb  c
0  1       2  3
1  4       5  6
2  7       8  9
>>> df.rename(columns={'a':'a_egg', 'b':'b_spam'})
   a_egg  b_spam  c
0      1       2  3
1      4       5  6
2      7       8  9
```

任意の列を変更するなら`rename()`を利用した方がいい。

```python
>>> df.columns
Index(['a', 'b', 'c'], dtype='object')
>>> df.columns = ['eggs', 'ham', 'spam']
>>> df
   eggs  ham  spam
0     1    2     3
1     4    5     6
2     7    8     9
```

すべての列名を現状と全く異なる文字列にガラッと変えたい、とかなら`columns`に直接列名を設定した方が早い。

## reference

1. [pandas.DataFrameの行名・列名の変更](https://note.nkmk.me/python-pandas-dataframe-rename/)
