# リストについて

## detail

```python
thislist = ["apple", "banana", "cherry"]
print(thislist)
```

角カッコ`[]`で囲われた0個以上の要素を持つオブジェクトをリストといい、要素には文字列だったり数値などが指定できる。

```python
>>> hoge = [1, 2, 3]
>>> hoge
[1, 2, 3]
```

角カッコで要素を列挙するのが、基本的なリストの生成方法。

```python
>>> [1 for _ in range(3)]
[1, 1, 1]
>>> [x * 2 for x in range(3)]
[0, 2, 4]
```

リスト内包表記でも生成可能。

```python
>>> ['hoge'] * 3
['hoge', 'hoge', 'hoge']
>>> ['hoge'] + ['fuga', 'piyo']
['hoge', 'fuga', 'piyo']
>>> ['hoge', 'fuga', 'piyo'] * 3
['hoge', 'fuga', 'piyo', 'hoge', 'fuga', 'piyo', 'hoge', 'fuga', 'piyo']
>>> ['hoge', 'fuga', 'piyo'] + [1] * 3
['hoge', 'fuga', 'piyo', 1, 1, 1]
```

リストの要素は、`+`や`*`で追加できる。

```python
>>> [1, 2, 3] - [1]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for -: 'list' and 'list'
>>> [1, 2, 3] / [1]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for /: 'list' and 'list'
```

とは言え、さすがにこういうのは無理だけど。


## reference

1. [Data Structures](https://docs.python.org/3/tutorial/datastructures.html)
2. [Python Lists](https://www.w3schools.com/python/python_lists.asp)
