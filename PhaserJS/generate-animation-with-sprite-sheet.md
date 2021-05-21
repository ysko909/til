## スプライトシートを使ってアニメーションを構築する

### detail

アセットとして取り込んだスプライトシートを使って、アニメーションを生成してみる。ここではアニメーションを生成して、キー入力に応じて動作するようなロジックを記述する。

```typescript

public preload(): void {
    let load_assets = ['spriteHoge', 'white_and_black'];

    [...load_assets].forEach(asset => {
        let pngPath = 'assets/spritesheet/' + asset + '/' + asset + '.png';
        let jsonPath = 'assets/spritesheet/' + asset + '/' + asset + '.json';
        this.load.atlas(asset, pngPath, jsonPath);
    });


}

public create(): void {
  this.player = this.add.sprite(300, 300, 'spriteHoge', '01_forward_neutral1',);

  this.anims.create({
      key: 'walkFoward', frames: [
          { key: 'spriteHoge', frame: '03_forward_walk1' },
          { key: 'spriteHoge', frame: '02_forward_neutral2' },
          { key: 'spriteHoge', frame: '04_forward_walk2' },
      ], frameRate: 10, repeat: -1
  });

  this.cursors = this.input.keyboard.createCursorKeys();
}
```

まず、スプライトシートを取り込んでオブジェクトを任意の場所に配置しておく。

アニメーションは`scene.anims.create()`で作成する。`key`はアニメーションごとの識別子を、`frames`はアニメーションで利用するフレームを配列で指定する。この配列はオブジェクトを要素とし、`{key: スプライトシート名, frame: スプライト名}`でアニメーションの各フレームを設定できる。`framerate`はアニメーションの速さを設定可能。`repeat`は「-1」に設定すると永久にリピートする。

スプライトとアニメーションの設定はこれだけでよい。

最後の1文は、キーボードの方向キー入力を受け付ける設定。

```typescript
public update(): void {

    if (this.cursors.down.isDown) {
        this.player.anims.play('walkFoward', true);
        this.player.y += 2;
    }

    else {

        this.player.anims.stop();
    }
}

```

キーボード入力に対応してアニメーションさせるには、`update()`関数内でアニメーションを動作させる必要がある。この場合は、トリガーはキー入力になるので、任意のキー入力に対しアニメーションが動作するよう記述してやればいい。`任意のオブジェクト.anims.play()`で指定されたオブジェクトが設定されたアニメーションを実行する。ここでは`this.player`というオブジェクトが、アニメーションしながらY座標を移動する。

キー入力がされていない場合は、`anims.stop()`とすることでアニメーションを中止できる。

上記では1つのアニメーションしか指定していないが、方向キーそれぞれに異なるアニメーションを設定することが可能。

### reference

1. [Animation](https://rexrainbow.github.io/phaser3-rex-notes/docs/site/animation/)
2. [How to create sprite sheets for Phaser 3 with TexturePacker ](https://www.codeandweb.com/texturepacker/tutorials/how-to-create-sprite-sheets-for-phaser3)

