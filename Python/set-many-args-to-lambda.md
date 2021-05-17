# Pythonでラムダ式に複数の引数を設定する方法

## detail

ラムダ式に複数の引数を設定する場合、単純に「引数設定の際に複数の引数を記述」すればいい。`def`による関数宣言を同様に、引数の初期値設定が可能。

```python
>>> hoge = lambda a, b=2: a * b
>>> hoge(1)
2
>>> hoge(1, 3)
3
>>> hoge()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: <lambda>() missing 1 required positional argument: 'a'
>>> hoge(b=100, a=4)
400
>>> 
```

上記の場合、引数bに2が初期設定されているため、引数aだけ指定してやれば実行可能。引数が指定されなければエラーになるのも、`def`による関数宣言と同じ。

なお、ラムダ式を記述する際に式に対し、**任意の名前を付けるのはPEP8的にご法度**。なので、PEP8に準拠したLintではエラーになる。

```python
>>> tetens = lambda x: 6.11 * (10 ** (7.5 * x / (x + 237.3)))
>>> tetens(20)
23.389356843099335
```

ここではラムダ式に`tetens`という名前をつけ、実行する際も`tetens(引数)`という記述方法で実行している。PEP8からすると、この書き方をするくらいなら「defで関数宣言しろよ」ということらしい。まぁ気持ちはわかる。一応、上記のように記述と実行自体は可能なので、構文エラーとかではなくあくまでPEP8のコーディング規約からするとダメという話。インタプリタ環境で記述するには便利・・・かなぁ。1行で済むようなら`def`使っても大して変わらないと思う。読みやすさはコードの内容にもよるが、複雑な処理なら確実に`def`に分がある。

## reference

1. [Pythonのlambda（ラムダ式、無名関数）の使い方](https://note.nkmk.me/python-lambda-usage/)
2. [PEP8](https://www.python.org/dev/peps/pep-0008/)
