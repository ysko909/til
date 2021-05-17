# matplotlibで描画したグラフのX軸とY軸の幅を調査する方法

## detail

matplotlibでグラフを描画した際、グラフのX軸とY軸がそれぞれどのくらいのレンジ（幅）で描画されているかを調べたい場合は、`xlim()`と`ylim()`を実行する。

```python
sns.lineplot(x=df.index, y=df['hoge'])
print(plt.xlim())
print(plt.ylim())
plt.show()
```

```console
(18107.05, 18479.95)
(1.0695084678691622, 12.550942168141958)
```

グラフを描画する際、とくに指定しなければmatplotlibが自動で、指定されたデータの最大値および最小値が十分に収まるような、適当な幅をX軸とY軸に設定しグラフを出力する。この際、X軸とY軸の出力幅を変えたければ`xlim(x軸の最小値, x軸の最大値)`や`ylim(y軸の最小値, y軸の最大値)`といった指定を行う。

ところが、日付などを出力の軸にしていえる場合、その最大値や最小値の具体的な数値がわからない場合がある。データ上の最大値や最小値を参照してもよいが、たとえば「出力したグラフに補助線を引きたい」という場合、補助線を引きたい座標の**具体的な数値**を指定しなければならない。日付はあくまでも**見た目のラベルであって座標ではない**ので、日付指定による補助線の座標指定はできない。その場合、描画されているグラフのX軸とY軸の最大値と最小値の具体的な数値を取得し、その数字に則って座標を決めることで補助線の座標を指定できる。

```python
import matplotlib.patches as patches

sns.lineplot(x=df.index, y=df['hoge'])
e = patches.Rectangle(xy=(18105, 3) , width=18470, height=3, ec='#523245', fill=True, alpha=0.3, color='r')
ax = plt.axes()
ax.add_patch(e)
plt.show()
```

上記では、取得した座標に則って四角を`Rectangle()`関数で描画し、グラフに出力している。

## reference

1. [Matplotlibで円や長方形などの図形を描画](https://note.nkmk.me/python-matplotlib-patches-circle-rectangle/)
