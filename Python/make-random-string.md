# Pythonでランダムな文字列を生成する方法

## detail

必要があったので、Pythonでランダムな文字列を生成するプログラムを作った。ホントにただのランダムな文字列。

```python
import random, string

def get_random_string(length=8, uppercase=True, lowercase=True, digits=True):
    seed_string = ''

    if uppercase:
        seed_string += string.ascii_uppercase
    if lowercase:
        seed_string += string.ascii_lowercase
    if digits:
        seed_string += string.digits
    
    if len(seed_string) > 0:
        result = ''.join([random.choice(seed_string) for i in range(length)])
    else:
        result = ''

    return result
```

内容は簡単で、引数により指定された内容で文字列を一度生成して、その文字列から指定された回数分1文字ランダムで抽出する。

```python
>>> import string
>>> string.ascii_lowercase
'abcdefghijklmnopqrstuvwxyz'
>>> string.ascii_uppercase
'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
>>> string.digits
'0123456789'
```

`string`で始まる部分は、**文字列定数**と呼ばれている定数で、それぞれ大文字や小文字が格納されている。これらの定数を連結して抽出元の文字列を生成している。

```python
>>> get_random_string()
'2TsENwLT'
>>> get_random_string(length=100, uppercase=True, lowercase=False, digits=True)
'0LMQ5Z28CHRQUGYMLHVVQX4VJZFZZXR4OBO13AZNCN6RQ4CTLHGVOS26UH6BKSF5EQ1ZJC6MQDQO53R92JEXSQNLWVMWCUELXQ66'
```

- 実行するときは、引数を一切指定しなければ大文字小文字と数字で8文字分生成する。
- 大文字や小文字、数字の出力を抑制したい場合は`False`を指定すればいい。文字列長はデフォルトでは8文字。
- すべての文字を出力対象にしない場合は、文字列長の長さにかかわらず空文字を返す。

## reference

1. [pythonでランダム文字列の生成](https://hacknote.jp/archives/52068/)
1. [string --- 一般的な文字列操作](https://docs.python.org/ja/3/library/string.html)
