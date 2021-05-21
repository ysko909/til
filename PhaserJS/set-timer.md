## タイマーを設定する方法

### detail

`Phaser.Time.Clock`クラスを利用することで、時限式のイベントを仕込むことが可能。

```typescript
class GameScene extends Phaser.Scene {
    timerRepeat: Phaser.Time.Clock;
    timerLoop: Phaser.Time.Clock;
    timerOneShot: Phaser.Time.Clock;
  
    constructor() {
        super('gameScene');
    }

    preload() {

    }

    create() {
      this.timerRepeat = this.time.addEvent({
        delay: 1000,
        callback: ()=>console.log('hoge'),
        callbackScope: this,
        repeat: 2,
      });

      this.timerLoop = this.time.addEvent({
        delay: 2000,
        callback: ()=>console.log('loop'),
        callbackScope: this,
        loop: true,
      });

      this.timerOneShot = this.time.delayedCall(3000, ()=>console.log('one shot'), this
      );

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

`addEvent()`では、引数にタイマーの設定を記述したオブジェクトを渡す。`loop:true`にすれば延々とループするし、`repeat:n`とすればn+1回リピートする。なぜ1回足されるかというと、0回目からスタートするようで、`repeat:2`と指定した場合は合計で3回コールバック関数が実行される。`repeat:0`とした場合は1回のみの実行で終了、つまり単発の実行と同じになる。単発で実行する場合は`delayedCall()`で実装することも可能。こちらは、引数にオブジェクトではなく経過時間、コールバック関数、コールバックのスコープなどを指定する。設定した時間が経過すると、1度だけコールバック関数が実行される。

### reference

1. [Phaser.Time. Clock](https://photonstorm.github.io/phaser3-docs/Phaser.Time.Clock.html)
2. [Timer](https://rexrainbow.github.io/phaser3-rex-notes/docs/site/timer/)
