## 複数のオブジェクトを管理するにはContainerオブジェクトを利用する

### detail

スプライトやその他のゲームオブジェクトを複数配置した際、これらを1つのオブジェクトとして管理したい場合にContainerオブジェクトにまとめる方法がある。たとえばアイテム画面などで、左側にカテゴリの一覧を表示し、右側に各カテゴリのアイテムを個数とともに表示し、その下側に選択したアイテムの説明を表示するという画面を作るとする。この場合、カテゴリ・アイテム・説明の各欄は別に管理するよりまとめて1つのオブジェクト「アイテムメニュー」として管理した方が、メニュー画面を生成するのも破棄するのも個別に実行する必要がないので簡単。

ここでは、`Container`オブジェクトへの子要素登録と破棄方法について記述する。

```typescript
class Rectangles extends Phaser.GameObjects.Container{
  constructor(scene){
    super(scene);
    
    this.scene = scene;
    this.scene.add.existing(this);
    
    const {width, height} = this.scene.game.canvas;
    
    const margin = 10;
    let background = this.scene.add.rectangle(0 + margin, 0 + margin, width - margin, height - margin, 0x111111);
    background.setOrigin(0, 0);
    
    this.add(background); // add gameobject to container
    
    let recs = [];
    let ellipses = [];
    
    for (let i=0; i<10; i++){
      
      let rec = this.scene.add.rectangle(Phaser.Math.Between(0, width), Phaser.Math.Between(0, height), 100, 100, 0x542342);
      rec.setOrigin(0, 0);
      recs.push(rec);
      
      let ellipse = this.scene.add.ellipse(Phaser.Math.Between(0, width), Phaser.Math.Between(0, height), 100, 100, 0x12a797);
      ellipse.setOrigin(0, 0);
      ellipse.setInteractive();
      ellipse.on('pointerdown', ()=>{
        ellipse.input.enabled = false;
        // ellipse.setVisible(false);
        ellipse.destroy();
      });
      ellipses.push(ellipse);
      
    }
     
    this.add(recs); // add rectangles to container
    this.add(ellipses);
    
    this.timerOneShot = this.scene.time.delayedCall(5000, ()=>{
      console.log(ellipses.length);
      this.destroy();
    }, this);
    
  }
    
}

class GameScene extends Phaser.Scene {
    constructor() {
        super('GameScene');
    }

    preload() {

    }

    create() {

      this.container = new Rectangles(this);
      
    }
  
  update(){
    
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

上記のコードは、実行すると円と四角を10個ランダムな位置に生成する。生成から5秒後、背景の四角を含んで画面上のオブジェクトが消失する。この際、画面からオブジェクトを消す処理をしているのは、複数のゲームオブジェクトを個別に削除しているわけではなく、それらを登録したコンテナを削除する処理を**1回だけ実行している**。複数のオブジェクトをコンテナに登録することで、削除する際はコンテナを1回破棄すればいいのでオブジェクトの管理が楽になる。

コンテナへゲームオブジェクトを登録するには、まずゲームオブジェクトをシーンに対して生成してから`add()`を実行する。上記だと`this.add(background);`などがその処理に当たる。引数は単一のオブジェクトに限らず、配列を渡してもよい。これでコンテナにゲームオブジェクトが登録できるので、コンテナに対し`destroy()`を実行してコンテナを破棄すると、登録済みのゲームオブジェクトも一緒に破棄できる。

また、円のオブジェクトはコンテナを破棄するタイミングまでクリックを受け付けており、クリックされると画面から消える。これは**個別のオブジェクトに対し実行している**のが前述のコンテナの処理と異なるところ。どちらも`destroy()`というメソッドを実行することで実装できる。

### reference

1. [Phaser.GameObjects.Container](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Container.html)
2. [Phaser.GameObjects.Rectangle](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Rectangle.html)
3. [Phaser.Input.InputPlugin](https://photonstorm.github.io/phaser3-docs/Phaser.Input.InputPlugin.html)
4. [Container To Render Texture](https://www.phaser.io/examples/v3/view/game-objects/render-texture/container-to-render-texture)
5. [Registry Data Exchange Es6](https://phaser.io/examples/v3/view/scenes/registry-data-exchange-es6)
   ちなみにこのサンプルはなにか挙動がおかしくて、「赤いオブジェクトをクリックしたらペナルティ」なはずなのだが、ペナルティの処理が正常に動作しておらず通常と同じく得点として処理されている。原因は`if`文の条件が`===255`になっているせいで、試しに`>255`へ修正してみると想定通りの動作をする。判定対象の変数に、小数を含む値が含まれているせいかもしれないが未確認。
6. [setInteractive() on a whole group](https://www.html5gamedevs.com/topic/36248-setinteractive-on-a-whole-group/)
