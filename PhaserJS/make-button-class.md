## ボタンのクラスを作成する方法

## detail

任意の文字列と押されたときの挙動を書いたコールバック関数などを受け取って、画面にボタンを配置するクラスを作った。

```typescript
type Props = {
  width: number;
  height: number;
  onClick: Function;
  align: string;
  fontSize: number;
  color: string;
}

class OriginalButtonClass extends Phaser.GameObjects.Container{
  
  text: Phaser.GameObjects.Text;
  container: Phaser.GameObjects.Rectangle;
  
  constructor (scene: Phaser.Scene, x:number, y:number, text:string, props: Props){
    super(scene, x, y);
    
    const{
      width = 90,
      height = 40, 
      onClick,
      align = 'center', 
      fontSize = 30, 
      color = 'black'
    } = props;
    
    this.scene = scene;
    this.scene.add.existing(this);
    
    this.setSize(width, height);
    this.setInteractive({useHandCursor: true});
    
    const alignLeft = align === 'left';
    
    this.text = scene.add.text(alignLeft ? -width / 2 + 0 : 0, -1, text, {align, fontSize, color}).setOrigin(alignLeft ? 0:0.5, 0.5).setPadding(0, 2, 0, 0);
    this.text.setColor(color);
    
    this.container = scene.add.rectangle(0, 0, width, height);
    this.container.setStrokeStyle(1, 0xffffff).setOrigin(alignLeft ? 0 : 0.5, 0.5);
    
    this.add([this.container, this.text])
    this.on('pointerover', () => {
      this.updateButton('pointer over', 'red');
    });
    
    this.on('pointerout', () => {
      this.updateButton('pointer out', color);
    });
    
    this.on('pointerdown', ()=>{
      this.updateButton('pointer down', 'blue');
      this.scene.tweens.add({
        targets: this,
        scaleX: 1.1,
        scaleY: 1.1,
        duration: 100,
        yoyo: true,
        repeat: 2,
        ease: 'Sine.easeInOut',
      });
    });
    
    this.on('pointerup', () => {
      this.updateButton('pointer up','yellow');
      onClick();
    });
    
  }
  
  updateButton =(message: string, color: string)=>{
    console.log(message);
    this.text.setColor(color);
  }

}

class GameScene extends Phaser.Scene {
    constructor() {
        super('gameScene');
    }

    preload() {

    }

    create() {
      this.button = new OriginalButtonClass(this, 200, 200, 'hoge', {
        onClick: ()=>{
          console.log('clicked.');
        }
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

game.scene.add('gameScene', gameScene);

game.scene.start('gameScene');
```

クリックしたときのTweenも設定してみた。

ボタンを押したときの機能は関数で渡すので、ボタンのクラス内には特段の機能は記述していない。必要があれば追記するか、このクラスを継承すればいい。

## reference

1. [Buttons In Phaser 3](https://snowbillr.github.io/blog/2018-07-03-buttons-in-phaser-3/)
2. [Phaser3でボタンクラスを作る](https://qiita.com/S-Kaito/items/85a1c90c86e61156c87d)
