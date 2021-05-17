# リストのソート方法まとめ

## detail

Pythonでリストをソートする書き方を、DataFrameのソートと混同するのでメモ。

```python
>>> hoge = ['foo', 'bar', 'baz']
>>> sorted(hoge)
['bar', 'baz', 'foo']
>>> hoge
['foo', 'bar', 'baz']
>>> hoge.sort()
>>> hoge
['bar', 'baz', 'foo']
```

- Pythonでリストをソートする場合、`sorted()`と`sort()`の2通りがある。
- 違いは新しいリストを返す（`sorted()`）か、もとのリストを変える（`sort()`）かという点。どちらがいいかは、ケースバイケースだと思う。とりあえず`sorted()`を覚えておけばタプルや文字列にも使える（ただし、リストを返すので変換が必要）から、潰しがきくこっちだろうか。

```python
>>> fuga = 'hogefugapiyo'
>>> sorted(fuga)
['a', 'e', 'f', 'g', 'g', 'h', 'i', 'o', 'o', 'p', 'u', 'y']
>>> ''.join(sorted(fuga))
'aefgghioopuy'
>>> piyo = ('foo', 'bar', 'baz')
>>> sorted(piyo)
['bar', 'baz', 'foo']
>>> tuple(sorted(piyo))
('bar', 'baz', 'foo')
>>> sorted(piyo, reverse=True)
['foo', 'baz', 'bar']
>>> tuple(sorted(piyo, reverse=True))
('foo', 'baz', 'bar')
```

- 文字列に対し`sorted()`を実行すると、リストで結果が返ってくるので`join()`を使って連結する。この際、空文字`''`を使って連結すれば単純な文字列に変換できるし、カンマやハイフンなどで連結することも可能。
- タプルに対し`sorted()`を実行すると、こちらもリストで結果が返ってくるので、`tuple()`を使ってタプルに変換する。
- なお、`sorted()`の引数に`reverse=True`を指定すると降順になる。

ちなみに、DataFrameやSeriesをソートするのは`sort_values()`。`sort_index()`は行や列のインデックスでソートする。

## reference

1. [Pythonでリストをソートするsortとsortedの違い](https://note.nkmk.me/python-list-sort-sorted/)
1. [pandas.DataFrame, Seriesをソートするsort_values, sort_index](https://note.nkmk.me/python-pandas-sort-values-sort-index/)
