## キー番号をキー名に変更する方法

### detail

Phaserにおいて、キーボードのどのキーが押されたのかは、数値であるキーナンバーで管理されている。ただ、プログラムを書く上では数値のキーナンバーよりも文字列であるキー名で管理したい。よって、`Phaser.Input.Keyboard.KeyCodes`という名前のオブジェクトで、キー名とキーナンバーが紐づけられている。[ここ](https://github.com/photonstorm/phaser/blob/v3.51.0/src/input/keyboard/keys/KeyCodes.js)を参照してみると、オブジェクトとして宣言されていることがわかる。

```javascript
var KeyCodes = {

    /**
     * The BACKSPACE key.
     * 
     * @name Phaser.Input.Keyboard.KeyCodes.BACKSPACE
     * @type {number}
     * @since 3.0.0
     */
    BACKSPACE: 8,

    /**
     * The TAB key.
     * 
     * @name Phaser.Input.Keyboard.KeyCodes.TAB
     * @type {number}
     * @since 3.0.0
     */
    TAB: 9,

    /**
     * The ENTER key.
     * 
     * @name Phaser.Input.Keyboard.KeyCodes.ENTER
     * @type {number}
     * @since 3.0.0
     */
    ENTER: 13,
    
    ...以下に続く
```

一部を抜粋したが、上記のように`キー名: キーナンバー`の形で登録されており、プログラム中でキーボード入力を判断したい場合はキーナンバーとキー名の両方が利用できる。

ただし、これはあくまで人間に見やすくするためのもので、`Phaser.Input.Keyboard`オブジェクトの中ではキーナンバーで管理されている。基本的にはあまり気にすることはないが、デバッグのときなどに「キーナンバーからキー名を取得したい」ことがある。その都度上記のオブジェクトを参照するのも面倒なので、キーナンバーからキー名を取得する関数を作る。

```typescript
const getKeyNameByKeyNumber=(keyNumber)=>{
  let keyName = Object.keys(Phaser.Input.Keyboard.KeyCodes).filter(value => {
    return Phaser.Input.Keyboard.KeyCodes[value] === keyNumber;
  });
  return keyName[0];
}

class RootGameScene extends Phaser.Scene {
  keyShift: Phaser.Input.Keyboard.Key;
  keysFunction: Phaser.Input.Keyboard.Key;
  
    constructor(sceneName : string) {
        super(sceneName);
    }

    create() {
      this.keyShift = this.input.keyboard.addKey('SHIFT');
      this.keyShift.on('down', ()=>{
        // キーナンバー
        console.log('Key Number: ' + this.keyShift.keyCode.toString());
        // キーナンバーからキー名を取得する
        console.log('Key Name: ' + getKeyNameByKeyNumber(this.keyShift.keyCode));
      });
      
      this.keysFunction = this.input.keyboard.addKeys('z, x');
      this.keysFunction.z.on('down', ()=>console.log('Key Name: ' + getKeyNameByKeyNumber(this.keysFunction.z.keyCode)));
      this.keysFunction.x.on('down', ()=>console.log('Key Name: ' + getKeyNameByKeyNumber(this.keysFunction.x.keyCode)));
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

`getKeyNameByKeyNumber()`という関数がそれに当たる。引数にキーナンバーを指定されると、それに該当するキー名を`Phaser.Input.Keyboard.KeyCodes`の中から検索して返す。現状だと、存在しないキーナンバーが指定された場合は`undefined`が返される。

`Phaser.Input.Keyboard.Key`オブジェクトには`keyCode`プロパティが存在し、このプロパティには`addKey()`で登録したキーのキーナンバーが格納されている。キーナンバーだと何のキーなのか判別しにくいので、この関数を使ってキー名に変換してやればデバッグするときにちょっとだけ楽になる。

### reference

1. [Phaser.Input.Keyboard.KeyCodes](https://photonstorm.github.io/phaser3-docs/Phaser.Input.Keyboard.KeyCodes.html)
2. [Object.keys()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)
3. [Array.prototype.filter()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
