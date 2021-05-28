## Phaserでの基本形

### detail

ES6の形式でPhaserを記述する場合、`class`によるクラスの宣言とPhaserクラスの継承がメインの書き方になる。とくに、Sceneは進行上欠かせない要素なので、`Phaser.Scene`の継承と`super()`によるコンストラクタの記述が必要になると思われる。もうこれは、この形を基本形として覚えてしまってもいいかもしれない。

```javascript
class GameScene extends Phaser.Scene {
    constructor() {
        super('GameScene');
    }

    preload() {

    }

    create() {

        let r1 = this.add.rectangle(200, 200, 100, 150, 0x6666ff);

        this.tweens.add({
        targets: r1,
        scaleX: 0.25,
        scaleY: 0.5,
        yoyo: true,
        repeat: -1,
        ease: 'Sine.easeInOut',
        });

    }
}

let gameScene = new GameScene();

let config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    backgroundColor: '#e0e0e0'
};

let game = new Phaser.Game(config);

game.scene.add('GameScene', gameScene);

game.scene.start('GameScene');
```

この記述内容だと、ただ画面に表示されるだけでそれ以上のことは何もない。ただ、根幹の基本形としてとくにクラスの部分は覚えてしまった方がいいかも。

### reference

1. [Phaser.Scene](https://photonstorm.github.io/phaser3-docs/Phaser.Scene.html)
