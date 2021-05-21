## 視差を利用した背景イメージを表示する

### detail

webページのデザインにParallaxというものがある。Parallaxとは「視差」を意味していて、部分ごとに動くスピードや方向に差をつけるようなデザインのこと。これをPhaserで表現する。

もう少し詳しく言うと、たとえば横スクロールアクションゲームにおいて、「超遠い位置にある太陽の位置はプレイヤーに関わらずスクロールしない」けど、「中間に位置する山はプレイヤーの移動速度の半分でスクロールする」し、「手前に位置する地面やブロックなどのオブジェクトはプレイヤーとほぼ変わらない速度でスクロールする」というように、レイヤーやオブジェクトごとに移動速度を個別に設定する。このように移動速度に差を施すことで、Parallaxつまり視差を実現し奥行きを表現する。

じゃあ、どうやって移動速度に差を施すかというと、**それ用のメソッドが用意されている**。

```typescript
this.add.image(0, 0, 'backgroundImage').setOrigin(0.5, 0.5).setScrollFactor(0.25);
```

`setScrollFactor(0.25)`の部分がまさにそれで、引数に本来の移動速度とどのくらい差をつけるか、小数で指定する。

背景画像に対しこのメソッドを使って1を指定した場合、カメラの動きと完全に同期する。つまり、プレイヤーをカメラが追うなら、プレイヤーと完全に同期して背景画像もスクロールする。一切の視差がない、ということだ。対して、0を指定した場合はカメラが動いても追随しない。まったく変わらない（変えたくない）風景なら0を設定する。

たとえば最背面に位置する背景画像には`setScrollFactor(0)`、奥から2番目の風景画像には`setScrollFactor(0.25)`、奥から3番目の背景画像には`setScrollFactor(0.5)`とすると、背景画像ごとに視差を個別に設定できる。これで、奥行き感を表現可能だ。

### reference

1. [Phaser.GameObjects.Components. ScrollFactor ](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Components.ScrollFactor.html#setScrollFactor__anchor)
1. [Add Pizazz with Parallax Scrolling in Phaser 3](https://blog.ourcade.co/posts/2020/add-pizazz-parallax-scrolling-phaser-3/)
