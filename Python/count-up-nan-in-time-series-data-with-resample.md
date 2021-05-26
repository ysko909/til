# `resample`を使って時系列データに含まれる欠損値をカウントする

## detail

センサーなどを使って任意の単位時間あたりでデータを取得するとき、取得された結果は時系列データとなる。このとき、何らかの理由でデータが取得できないと、その部分は欠損値となる。今回は時系列データに含まれている欠損値について、「月ごとに集計すると各月でどのくらい欠損しているか」を確認できるような方法について記述する。

## reference

1. [pandasで時系列データをリサンプリングするresample, asfreq](https://note.nkmk.me/python-pandas-time-series-resample-asfreq/)
2. [pandasの時系列データにおける頻度（引数freq）の指定方法](https://note.nkmk.me/python-pandas-time-series-freq/)
