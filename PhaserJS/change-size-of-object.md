## ロードしたオブジェクトのサイズを変更する

### detail

Phaser3で画像のスケールを変更する場合、`scale`を使うと画像のサイズを拡大あるいは縮小できる。もうちょっと正確に言うと、`scale`だと縦横どちらも拡大あるいは縮小する。`scaleX`ならX軸、つまり横方向に拡大あるいは縮小する。`scaleY`ならY軸、つまり縦方向に拡大あるいは縮小する。

```javascript
export class MainScene extends Phaser.Scene {
    constructor() {
        super('mainScene');
    }

    preload() {
        this.load.image('hoge', 'assets/if/hoge.png');
        this.load.image('fuga', 'assets/if/fuga.png');
    }

    create() {

        let hoge = this.add.image(width - 100, height - 100, 'hoge');
        hoge.scale = 2; // 縦横どちらも2倍する

        let fuga = this.add.image(width - 200, height - 200, 'fuga');
        fuga.scaleX = 0.5; // 横方向に縮小する。
        
    }
}
```

上記では`add.image()`したオブジェクトに対して拡大あるいは縮小しているが、`add.sprite()`したオブジェクトでも同様にサイズ変更できる。

### reference

1. [Phaser.GameObjects.Image](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Image.html)
2. [Scaling in phaser3](https://phasergames.com/scaling-in-phaser-3/)

