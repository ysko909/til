# インスタンスメソッドとクラスメソッドとスタティックメソッドの違い

## detail

Pythonのメソッドには3種類存在する。

- インスタンスメソッド：インスタンスに紐づくメソッド。第1引数は必須で`self`とする。呼び出したとき、第1引数にインスタンスが自動的に設定される。
- クラスメソッド：クラスに紐づくメソッド。第1引数は必須で`cls`とする。呼び出したとき、第1引数にクラスが自動的に設定される。
- スタティックメソッド：クラスに紐づくメソッド。第1引数は**任意**でなくてもよい。インスタンスメソッドやクラスメソッドのように、第1引数に自動的にインスタンスやクラスが設定されるようなことはない。

このうち、使用頻度が一番高いのがインスタンスメソッド。クラスメソッドは、ライブラリに実装されたものを利用することはままあると思う。が、自分で作成したクラスにクラスメソッドとして実装するなら、その機能を関数として実装してしまった方が可読性の面から楽なので、わざわざクラスメソッドを利用するケースはあまり多くない。スタティックメソッドは関数でいいのでは・・・？

```python
>>> class Hoge:
...   foo = 99
...   def __init__(self):
...     self.foo = 1
...   def instance_method(self, arg):
...     return self.foo + arg
...   @classmethod
...   def class_method(cls, arg):
...     return cls.foo + arg
...   @staticmethod
...   def static_method(arg):
...     return Hoge.foo + (arg * 2)
... 
>>> hoge = Hoge()
>>> hoge.instance_method(1)
2
>>> hoge.class_method(1)
100
>>> hoge.static_method(1)
101
>>> Hoge.instance_method(1) # インスタンスメソッドはインスタンスを指定しないとクラス経由で呼び出せない
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: instance_method() missing 1 required positional argument: 'arg'
>>> Hoge.class_method(1)
100
>>> Hoge.static_method(1)
101
>>> Hoge.instance_method(hoge, 1) # クラス経由で呼び出す場合、インスタンスを引数に指定すればインスタンスメソッドを呼び出せる
2
>>> Hoge.instance_method(Hoge, 1) # クラス経由で呼び出す場合、クラスを引数に指定してもインスタンスメソッドを呼び出せる
100
>>> Hoge.class_method(hoge, 1) # クラスメソッドはインスタンスメソッドと異なり、インスタンスやクラスを引数に指定できない
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: class_method() takes 2 positional arguments but 3 were given
>>> Hoge.class_method(Hoge, 1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: class_method() takes 2 positional arguments but 3 were given
>>> Hoge.static_method(hoge, 1) # スタティックメソッドも、インスタンスやクラスを引数に指定できない
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: static_method() takes 1 positional argument but 2 were given
>>> Hoge.static_method(Hoge, 1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: static_method() takes 1 positional argument but 2 were given
>>> 
```

|名前|引数|呼び出し|備考|
|:-:|:-:|:-:|:-:|
|インスタンスメソッド|self|インスタンス経由の場合、呼び出したインスタンスが暗黙的に第1引数に設定される。クラス経由の場合、引数にインスタンスかクラスの明示的な指定が必要。||
|クラスメソッド|cls|インスタンス経由の場合、呼び出したインスタンスの**クラスが暗黙的に**第1引数に設定される。クラス経由の場合、呼び出したクラスが第1引数に暗黙的に設定される。|宣言時に`@classmethod`の記述が必要。|
|スタティックメソッド|なし|呼び出した際に指定した引数が設定される。第1引数にインスタンスやクラスを暗黙的あるいは明示的に指定することはできない。|宣言時に`@staticmethod`の記述が必要。|

先に記述したとおり、基本的にはインスタンスメソッドを使って、クラスメソッドを利用するくらいなら関数を宣言してしまった方がよい。これは、クラスメソッドを利用するより関数を利用するほうが、コード上シンプルになるからだ。可読性は保守性に結び付くので、複雑よりシンプルにすべきだしインデントは1段でも浅い方がいい。クラスメソッドを使うのなら、まずは関数で実装ができないかを検討して、どうしてもクラスメソッドでなければならない、メソッドをそのクラスに紐づけなければならない場合に実装を検討する。ただ、そんなシチュエーションがどれだけあるか・・・。

## reference

1. [Pythonで、呼び出し方によってメソッドの振る舞いを変える](https://qiita.com/masaru/items/5ebf2e96d6524830511b)
2. [Pythonのクラスメソッド（@classmethod）とは？使いどころとメソッドとの違いを解説](https://blog.pyq.jp/entry/Python_kaiketsu_190205)
3. [【Python】インスタンスメソッド、staticmethod、classmethodの違いと使い方](https://djangobrothers.com/blogs/class_instance_staticmethod_classmethod_difference/)
