## 角の丸い四角を描画するには`fillRoundedRect()`や`strokeRoundedRect()`を使う

### detail

`rectangle`だと単純な長方形だがそれでは味気ない、というときに使えるのが角の丸い長方形。これは`Phaser.GameObjects.Graphics.fillRoundedRect()`と`strokeRoundedRect()`を利用することで、描画が可能になる。`fill〜`の方が塗りつぶされた長方形、`stroke〜`の方が線のみで塗りつぶしなしの長方形を描画する。

```javascript
graphics = this.add.graphics();

graphics.fillStyle(0xffff00, 1);

//  32px radius on the corners
graphics.fillRoundedRect(32, 32, 300, 200, 32);
```

`rectangle()`は`Shape`クラス配下だが、`fillRoundedRect()`は`Graphics`クラス配下で、扱いも`Shape`クラスとは若干異なる。

1. `fillRoundedRect()`の引数に塗りつぶしの色を指定する項目がない。
3. 第5引数は角の丸み（ラジアン）を調節する引数で、若い数字ほどただの長方形に近づく。
4. 角の丸み（ラジアン）は四隅それぞれで個別に設定可能。

塗りつぶしの色を引数で指定できないので、描画する前に`graphics.fillStyle(カラーコード, アルファ値);`で指定する必要がある。コレがないと、描画されない（内部では描画されているかもしれないが表示されない）ので注意。ちなみに`strokeRoundedRect()`の書式を設定する場合は、`lineStyle(線の太さ、カラーコード、アルファ値)`を利用する。

角の丸みはオプション扱いなので指定する必要はない。小さい長方形を描画する際、デフォルト設定だときれいな長方形にならない（蝶のように中心が凹んでしまう）ので、そういう場合のみ調節すればいいだろう。

```javascript
graphics.fillRoundedRect(360, 240, 400, 300, { tl: 64, tr: 22, bl: 12, br: 0 });
```

角の丸みは一律同じ値を設定することも可能だが、`{ tl: 1, tr: 2, bl: 3, br: 4 }`というように`Top-Left`・`Top-Right`・`Bottom-Left`・`Bottom-Right`をまとめたオブジェクトとして渡す事が可能。これにより、角の丸みを個別に設定できる。

### reference

1. [Phaser.GameObjects. Graphics](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Graphics.html#fillRoundedRect__anchor)
2. [Fill Rounded Rectangle](https://www.phaser.io/examples/v3/view/game-objects/graphics/fill-rounded-rectangle)

