## 特定のキーが押された場合に任意の処理を実行するコード

### detail

シーンごとに、任意のキーを登録しそれぞれについて実行したい処理を記述できる。キーの登録は個別でも複数でも可能。

```typescript
class RootGameScene extends Phaser.Scene {
  keyShift: Phaser.Input.Keyboard.Key;
  keysFunction: Phaser.Input.Keyboard.Key;
  
    constructor(sceneName : string) {
        super(sceneName);
    }

    create() {
      // 個別登録
      this.keyShift = this.input.keyboard.addKey('SHIFT');
      this.keyShift.on('down', ()=>{
        let keyName = Object.keys(Phaser.Input.Keyboard.KeyCodes).filter(value => {
          return Phaser.Input.Keyboard.KeyCodes[value] === this.keyShift.keyCode;
        });
        console.log(keyName[0]);
      });
      
      // 複数登録
      this.keysFunction = this.input.keyboard.addKeys('z, x');
      // 複数登録した場合はキーごとにプロパティが作成される
      this.keysFunction.z.on('down', ()=>console.log('Z key down'));
      this.keysFunction.x.on('down', ()=>console.log('X key down'));
    }
}

class GameScene extends RootGameScene {
  constructor(){
    super('GameScene')
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

キーの登録は、`scene.input.keyboard.addKey(キーコード)`で個別に、`scene.input.keyboard.addKeys('キーコード, キーコード, ...')`で複数のキーに対し処理が可能。

キーの登録が終わったら、キーごとにイベントを記述する。`on()`で引数にイベント（`down`だったり`up`だったり）と実行したい処理（上記だと`console.log('Z key down')`の部分とか）を記述する。これにより、登録したキーに対し`down`などのイベントが発生した場合に、特定の処理を実行することができる。

### reference

1. [Phaser.Input.Keyboard.KeyCodes](https://photonstorm.github.io/phaser3-docs/Phaser.Input.Keyboard.KeyCodes.html)
2. [Phaser.Input.Keyboard.Key](https://photonstorm.github.io/phaser3-docs/Phaser.Input.Keyboard.Key.html)
3. [Keyboard events](https://rexrainbow.github.io/phaser3-rex-notes/docs/site/keyboardevents/)
