## スプライトシートの使い方

### detail

作成したスプライトシートは、シーンにインポートしてアセットとして設定することで、キー入力などのイベントに対し特定のアクションを実行するよう設定できるようになる。

ただの画像ファイルをロードし、スプライト扱いで画面に表示させるには下記の方法でいい。

```javascript
preload() {

    this.load.image('beball', 'assets/sprites/beball1.png');
    this.load.image('atari', 'assets/sprites/atari400.png');
    this.load.image('bikkuriman', 'assets/sprites/bikkuriman.png');

}

create() {

    this.add.sprite(200, 300, 'beball');
    this.add.sprite(500, 300, 'atari');
    this.add.sprite(800, 300, 'bikkuriman');

}
```

### reference

1. [How to create sprite sheets for Phaser 3 with TexturePacker](https://www.codeandweb.com/texturepacker/tutorials/how-to-create-sprite-sheets-for-phaser3)
2. [Tiled Generated Map with Phaser 3](https://medium.com/@junhongwang/tiled-generated-map-with-phaser-3-d2c16ffe75b6)
3. [Load Atlas With Local Json](https://phaser.io/examples/v3/view/loader/texture-atlas-json/load-atlas-with-local-json#)
