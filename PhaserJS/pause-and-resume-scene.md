# ポーズしたり元のシーンに戻ったり

## detail

あるシーンにおいてポーズ機能を持たせる場合、`pause`というまんまな名前のメソッドがあるので、これを使う。

```typescript
class FirstScene extends Phaser.Scene {
  
  constructor() {
    super('firstScene');
  }

  create() {
    let text = this.add.text(100, 100, 'first scene.');
    text.setFontSize(30);
    text.setInteractive();
    
    text.on('pointerdown', ()=>{
      this.scene.launch('secondScene');
      this.scene.pause();
      
      this.events.on('pause', function () {
        console.log('Scene A paused');
      });

      this.events.on('resume', function () {
        console.log('Scene A resumed');
      });
    }, this);
    
  }
  
  update(){
    
  }
}

class SecondScene extends Phaser.Scene {
  constructor(){
    super('secondScene');
  }
  
  create(){    
    let text = this.add.text(200, 100, 'second scene.');
    text.setFontSize(30);
    text.setInteractive();
    
    text.on('pointerdown', ()=>{
      this.scene.resume('firstScene');
      this.scene.stop();
    }, this);

  }

}

let firstScene = new FirstScene();
let secondScene = new SecondScene();

let config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    backgroundColor: '#e0e0e0'
};

let game = new Phaser.Game(config);

game.scene.add('firstScene', firstScene);
game.scene.add('secondScene', secondScene);

game.scene.start('firstScene');
```

## reference

1. [Pause and Resume Game in Phaser 3](https://stackoverflow.com/questions/50988219/pause-and-resume-game-in-phaser-3)
2. [pause and resume](https://github.com/photonstorm/phaser3-examples/blob/master/public/src/scenes/pause%20and%20resume.js)
3. [Phaser 3 新機能 : Sceneの取り扱い① 遷移、並列実行](https://gpnotes.hatenablog.jp/entry/2018/11/19/161739#launch%E3%81%AB%E3%82%88%E3%82%8B%E4%B8%A6%E5%88%97%E5%AE%9F%E8%A1%8C)
