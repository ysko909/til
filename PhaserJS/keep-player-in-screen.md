## マップ内にプレイヤーを収める方法について

### detail

プレイヤーのスプライトに対し、キー入力に従って移動するようなロジックを実装したとする。このとき何も設定していないと、Tiledで作ったマップがたとえ100x100のサイズしかなかったとしても、プレイヤーのスプライトはそのサイズに関係なく移動できる。つまり、マップ外に飛び出してしまう。なんなら、ゲーム画面が100x100しかなかったとしても、スプライトはゲーム画面の外（つまり、不可視の部分）にまで行くことが可能なので、結果的にプレイヤーがゲーム画面から消えてしまう。

それを防ぎ、Tiledで作ったマップ内にプレイヤーを収めるための設定が存在する。

```javascript
    this.player = this.physics.add.sprite(300, 100, 'lain_normal', 'forward_neutral1',);

    this.physics.world.bounds.width = mapGround.width;
    this.physics.world.bounds.height = mapGround.height;
    this.player.setCollideWorldBounds(true);
```

`scene.physics.world.bounds`で、ゲーム世界のうち「どこまでを移動可能な範囲にするか」設定できる。ここでは、`mapGround`というTiledで作ったマップの幅と高さを設定している。また、これだけではマップの範囲をゲーム世界の移動可能範囲に指定しただけなので、`this.player.setCollideWorldBounds(true);`で、プレイヤーはゲーム世界の移動可能範囲においてのみ行動できるよう設定する。この設定により、プレイヤーはゲーム世界の壁に対して「衝突」するようになり、壁を超えることがなくなる。

これで、ゲーム世界の外へ飛び出してゲーム画面からプレイヤーがいなくなってしまう事態を避けることができる。

### reference

1. [How to Create a Turn-Based RPG in Phaser 3 – Part 1](https://gamedevacademy.org/how-to-create-a-turn-based-rpg-game-in-phaser-3-part-1/)
