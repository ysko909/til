# TIL - Python

Pythonに関することはここに書く。

## マルコフ連鎖用ライブラリMarkovify

### detail

[Markovify](https://github.com/jsvine/markovify)は、マルコフ連鎖を実装するライブラリ。マルコフ連鎖に特化したライブラリなので何でもできるライブラリではないものの、実装するだけならライブラリをインポートするだけでいいので楽。

### reference

1. [Markovify + Mecabによるマルコフ連鎖を使った文章生成](https://dai-py.hatenablog.com/entry/2019/04/09/023416)
1. [markovifyを使ってマルコフ連鎖でなろう小説のタイトルを自動生成するぞ！](https://murabitoleg.com/markov-narou/)

## 日本語専用形態素解析器Sudachi

### detail

日本語の形態素解析器だと[mecab](https://taku910.github.io/mecab/)が有名どころだが、[Sudachi](https://github.com/WorksApplications/SudachiPy)はワークスアプリケーションズで開発された、日本語用形態素解析器。辞書もPythonのライブラリ扱いされており、`pip`インストールできる。

`pip`でのインストールが終わると、コマンドライン上から利用できるようになる。

```console
$ echo "アルミ缶の上にあるミカン" | sudachipy
アルミ缶        名詞,普通名詞,一般,*,*,*        アルミ缶
の      助詞,格助詞,*,*,*,*     の
上      名詞,普通名詞,副詞可能,*,*,*    上
に      助詞,格助詞,*,*,*,*     に
ある    動詞,非自立可能,*,*,五段-ラ行,連体形-一般       有る
ミカン  名詞,普通名詞,一般,*,*,*        蜜柑
EOS
```

### reference

1. [SudachiPy](https://github.com/WorksApplications/SudachiPy)
1. [Pythonで形態素解析器Sudachiを使う](https://ohke.hateblo.jp/entry/2019/03/09/101500)
