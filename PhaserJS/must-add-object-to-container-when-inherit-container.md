# `Phaser.GameObjects.container`を継承するクラスは必ず`add()`すること

## detail

Phaserの便利なオブジェクトで`Container`というものが[ある](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Container.html)。これを使うことで複数のオブジェクトをまとめ、1つのオブジェクトとして扱える。

たとえば、1つの設定メニュー画面を作成すると仮定する。この中で、左側には設定のカテゴリを表示するダイアログ、右側には各設定カテゴリ中の項目と実際の設定値を表示する。このとき、左右のダイアログは出力内容が異なるため、別個に作成する必要がある（ダイアログの背景などは共通化できるかもしれないが）。ただし、画面に表示したり非表示にしたりする処理を、左右のダイアログについて**別個に**実行するのは無駄だし、処理間違いだったり処理の記述もれの可能性がありバグの発生原因になりかねない。そこで、左右のダイアログをコンテナに登録すれば、表示・非表示の処理をコンテナに対し実行すればいいので、結果的に1つのオブジェクトとして運用できる。こうすれば、バグの発生要因をそれなりに減少させることができる。

が、この「コンテナに登録する」という処理は**コンテナごとに行う**必要がある。

```typescript
class BackgroundScreen extends Phaser.GameObjects.Rectangle {

    constructor(scene: Phaser.Scene, backgroundColor: number = 0x000000, x: number = 0, y: number = 0) {

        let width = scene.game.canvas.width;
        let height = scene.game.canvas.height;

        super(scene, x, y, width, height, backgroundColor);

        this.setOrigin(0, 0); // Default is (0.5, 0.5)

        this.scene = scene;
        this.scene.add.existing(this);

    }
}

class Container1 extends Phaser.GameObjects.Container {
      constructor(scene: Phaser.Scene) {
        super(scene);
        this.scene = scene;
        this.scene.add.existing(this);

        let backgroundScreen = new BackgroundScreen(this.scene);
        this.add(backgroundScreen);
      }
}

class Container2 extends Phaser.GameObjects.Container {
      constructor(scene: Phaser.Scene) {
        super(scene);
        this.scene = scene;
        this.scene.add.existing(this);

        let backgroundScreen = new BackgroundScreen(this.scene, 0x5522e1, 200, 200);
        // this.add(backgroundScreen); // 本来はこのaddを実行すべき
      }
}

class BaseScene extends Phaser.Scene {
  
    constructor(sceneName: string) {
        super(sceneName);
    }

    preload() {

    }

    create() {

      
        let t1 = this.add.text(100, 100, 'hogefugapiyo');

        let container1 = new Container1(this);

        container1.add(t1);
      
      let container2 = new Container2(this);
      
      container1.add(container2);
      
      this.timerOneShot = this.time.delayedCall(3000, ()=>{
        container1.destroy();  // container1に含まれるオブジェクトがすべて削除される・・・はずが、青い四角だけ残存する
      }, this);
    }
}

class GameScene extends BaseScene {
  constructor(){
    super('gameScene');
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

game.scene.add('gameScene', gameScene);

game.scene.start('gameScene');
```

上記は、コンテナをコンテナに`add()`した場合としなかった場合で、挙動がどう異なるのかを検証したコード。

結論から言うと、`add()`しなかったコンテナでも画面表示までは実行されるが、コンテナに対する処理の`destroy()`が実行されなかった。

コンテナ`container1`の中には以下の要素を含んでいる。

- 黒い四角（クラス`BackgroundScreen`）
- 文字
- コンテナ`container2`

このうち、コンテナ`container2`の中で四角のオブジェクトを`add()`**すべきなのに実行していない**（検証のためにあえて実行していない）。クラス`Container2`の中で生成している青っぽい四角はクラス`BackgroundScreen`から生成されたインスタンスなので、コンテナ`container1`がもともと持っていたオブジェクトと同等（色と表示場所が違うだけ）。

これらのコンテナと文字オブジェクトを生成すると、それぞれ正常に画面に表示される。そこで、タイマーイベントを発生させ、表示から3秒後にコンテナ`container1`を`destroy()`を実行してオブジェクトを破棄してみる。本来であれば、コンテナ`container1`には画面に表示されているすべてのオブジェクトが含まれているので、`destroy()`実行後は画面上のオブジェクトがすべて破棄されて、背景しか表示されなくなるはず。ところが、`destroy()`を実行してみると、コンテナ`container2`内にある青っぽい四角が**画面に残存**する。

これは、前述のとおりクラス`Container2`においてコンテナにオブジェクトを登録する`add()`を実行していなかったため。実行していないと、コンテナ内のオブジェクトではなく個別のオブジェクトとして扱われるため、コンテナに対する処理（ここでは`destroy()`）を実行しても「関係ないよ」とスルーしてしまう。

では、`destroy()`が実行されないにもかかわらず、なぜ画面表示は行われたかというと、**シーンに対して**`scene.add.existing(this);`しているため。シーンへの登録処理を実行しているなら、コンテナへの登録云々は関係なく表示される。ただし、コンテナへの登録がされていないので、コンテナを対象にした処理に追随しない。上記のコードで青い四角を削除するとなると、四角のオブジェクトを直接`destroy()`するか、クラス`Container2`の中で`add()`を実行して、コンテナ内のオブジェクトとして扱うことにして`destroy()`の処理対象とする。まぁ、保守性の観点から言うと、コンテナに登録するほうがメリットが大きいだろう。

## reference

1. [Phaser.GameObjects.Container](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Container.html)
2. [laineus/unsung-kingdom/](https://github.com/laineus/unsung-kingdom/tree/master/src/class)
3. [Child order](http://phaser.io/examples/v3/view/game-objects/container/child-order#)
