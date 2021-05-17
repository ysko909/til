# 日本語専用形態素解析器Sudachi

## detail

日本語の形態素解析器だと[mecab](https://taku910.github.io/mecab/)が有名どころだが、[Sudachi](https://github.com/WorksApplications/SudachiPy)はワークスアプリケーションズで開発された、日本語用形態素解析器。辞書もPythonのライブラリ扱いされており、`pip`インストールできる。

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

`pip`でのインストールが終わると、コマンドライン上から利用できるようになる。

```python
from sudachipy import tokenizer
from sudachipy import dictionary

tokenizer_obj = dictionary.Dictionary().create()

s = '日本経済新聞は、日本経済新聞社の発行する新聞であり、広義の全国紙の一つ。略称は日経、または日経新聞。'

mode = tokenizer.Tokenizer.SplitMode.A
result = [m.surface() for m in tokenizer_obj.tokenize(s, mode)]

print(result)
# ['日本', '経済', '新聞', 'は', '、', '日本', '経済', '新聞', '社', 'の', '発行', 'する', '新聞', 'で', 'あり', '、', '広義', 'の', '全国', '紙', 'の', '一', 'つ', '。', '略称', 'は', '日経', '、', 'また', 'は', '日経', '新聞', '。']
```

Python上で利用するには、上記のようなコードになる。

## reference

1. [SudachiPy](https://github.com/WorksApplications/SudachiPy)
1. [Pythonで形態素解析器Sudachiを使う](https://ohke.hateblo.jp/entry/2019/03/09/101500)
