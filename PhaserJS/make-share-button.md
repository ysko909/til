## PhaserにTwitterへのシェアボタンを追加する

### detail

よくキャンペーンサイトとかでTwitterにシェアするためのボタンがあるけど、あれはTwitterに対してパラメータを仕込んだURLを渡すことで任意のツイートを生成するように実装できる。ボタンを押したときのイベントにコールバック関数を指定し、そのコールバック関数の中でURLを指定すればいい。

> http://twitter.com/share?url=[シェアするURL]&text=[ツイート内テキスト]&via=[ツイート内に含むユーザ名]&related=[ツイート後に表示されるユーザー]&hashtags=[ハッシュタグ]

ハッシュタグを生成できる`hashtags`は、カンマで区切ることで複数のハッシュタグを指定できる。これらの内容以外に`in-reply-to`でリプライ先を指定できるっぽいが、シェアに使うURLなので任意の誰かにリプライするケースはあまり想像つかないな。

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
          // console.log('clicked.');
          window.open('http://twitter.com/share?url=https://www.example.com&text=hoge, fuga and piyo!ほげ、ふが、ピ夜！？&hashtags=hogehoge,fugafuga,piyopiyo');
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

この前作ったボタンクラスのソースをちょっとだけ改変して、Twitterにシェアするボタンにしてみた。

```typescript
window.open('http://twitter.com/share?url=https://www.example.com&text=hoge, fuga and piyo!ほげ、ふが、ピ夜！？&hashtags=hogehoge,fugafuga,piyopiyo');
```

`window.open()`で指定したURLを開ける。ちなみに、これ自体はURLなのでテキストを入力する際にはクォーテーションなどで括ってはいけない。

### reference

1. [Web Intents](https://developer.twitter.com/en/docs/twitter-for-websites/web-intents/overview)
3. [Twitter シェアボタンの設置方法](https://note.com/ytk141/n/nc76dd06aaff4)
1. [Open url in a new new tab Safari](https://www.html5gamedevs.com/topic/40065-open-url-in-a-new-new-tab-safari/)
2. [Sharing a URL with a query string on Twitter](https://stackoverflow.com/questions/6208363/sharing-a-url-with-a-query-string-on-twitter)
