# Pythonでpandas.DataFrameに対し統計量を取得する`describe()`を実行するときの注意点について

## detail

ゼロを含んだDataFrameで`describe()`を実行したい場合、**ケースによっては**一度ゼロを除外する方がいい。ゼロが意味を持つデータならいいが、意味を持たない（欠損値と変わらない）ようなデータの場合、ゼロを含んだ状態で統計量を出すと、本来の意味とは異なる統計量が出てきてしまう。とくに最小値が必ずゼロになってしまうため、標準偏差もゼロを含んだ場合と含まない場合で差が出てくる。

```python
df.describe()
df[df > 0.0].describe()
```

`df`がゼロを含むデータであった場合、1行目と2行目の結果は異なる。`df[df > 0.0]`の場合、ゼロはデータとして抽出されないためNaNになる。`describe()`は**NaNを無視する**ため、2行目は1行目のゼロを含んだ結果と異なる結果を出力する。

## reference

1. [pandasのdescribeで各列の要約統計量（平均、標準偏差など）を取得](https://note.nkmk.me/python-pandas-describe/)
