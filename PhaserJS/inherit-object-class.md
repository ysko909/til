## オブジェクトのクラスを継承する

### detail

[Rectangle](https://phaser.io/examples/v3/view/game-objects/shapes/rectangle)などのオブジェクトは、そのクラスを継承して新しいオリジナルのクラスを宣言できる。

```typescript
class RandomRect extends Phaser.GameObjects.Rectangle {
  constructor(scene){
    super(scene, Phaser.Math.Between(0, 700), Phaser.Math.Between(0, 500));
    this.scene.add.existing(this);
    this.width = 100;
    this.height = 100;
    this.setFillStyle(0xff0000);
  }
}

class MainScene extends Phaser.Scene {
  constructor(){
    super('MainScene');
  }
  create(){
    [...Array(10).keys()].forEach(()=>{
      this.hoge = new RandomRect(this);
    });
  }
  update(){
    
  }
}

let mainScene: MainScene = new MainScene();

let config: object = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    backgroundColor: '#e0e0e0'
};

let game: Phaser.Game = new Phaser.Game(config);

game.scene.add('MainScene', mainScene);

game.scene.start('MainScene');
```

- `super()`による親コンストラクタが必要になるのは、`Phaser.Scene`クラスの継承と一緒。
- `Phaser.Math.Between()`は、引数に指定した値の間でランダムな値を返す。ここではとりあえず画面内に収まるよう値で、ランダムな位置に四角のオブジェクトを配置している。
- `this.scene.add.existing(this);`とすることで、指定したシーンにオブジェクトを追加する。これがないと、インスタンスが生成されても画面に表示されない。

### reference

1. [Phaser.GameObjects.Rectangle](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Rectangle.html)
