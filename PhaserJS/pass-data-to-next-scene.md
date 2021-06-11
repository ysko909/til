# 次のシーンにデータを引き継ぎ方法

## detail

次のシーンへ切り替える際、現在のシーンが保持しているデータを引き継ぎたいことがある。たとえば、毒状態のようなプレイヤーのステータス情報などがそれに当たる。これらを次のシーンに引き継ぐためには、シーン切り替えの際に引き継ぎたいデータを引数に指定する。引き継いだデータは遷移先のシーンで`init()`関数に引数として渡される。

```typescript
class FirstScene extends Phaser.Scene {
  
  constructor() {
    super('firstScene');
  }

  create() {
    this.data = {
      name: 'hoge',
      status: {
        new: {
          effect: 'none',
          hair: 'brown',
          shoes: 'black leather',
          level: 1,
          floor: 4,
          positionX: 123,
          positionY: 456,
        },
        old: {
          effect: 'ls',
          hair: 'brown',
          shoes: 'white sneaker',
          level: 1,
          floor: 3,
          positionX: 123.2,
          positionY: 456.1,
        }
      },
      layer: 3,
    };
  }
  
  update(){
    this.scene.start('secondScene', this.data);
  }
}

class SecondScene extends Phaser.Scene {
  constructor(){
    super('secondScene');
  }
   
  init(data){
    this.data = data;
  }
  
  create(){
    for (const [key, value] of Object.entries(this.data.status.new)){
      if (this.data.status.old[key] === this.data.status.new[key]){
        console.log('same: ' + `${this.data.status.old[key]}`);  
      } else {
        console.log('changed: ' + `${this.data.status.old[key]}` + ' to ' + `${this.data.status.new[key]}`);  
      } 
    }
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

シーン遷移を行う`scene.start()`で切り替え先のシーンを指定するとき、引き継ぎたいデータを同時に渡す。遷移先のシーンでは関数`init()`に引数として渡されるので、そのデータを用いて処理を継続できる。ここでは引き継いだ内容を比較して、データに変更があればその情報を出力するようなコードを書いている。

## reference

1. [How do you pass data to another scene?](https://www.html5gamedevs.com/topic/37745-how-do-you-pass-data-to-another-scene/)
2. [Object.entries()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)
