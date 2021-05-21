## JSON付きスプライトシートの使い方

### detail

ただの画像ファイルをロードし、スプライト扱いで画面に表示させるには下記の方法でいい。

```javascript
preload() {

    this.load.image('beball', 'assets/sprites/beball1.png');
    this.load.image('atari', 'assets/sprites/atari400.png');
    this.load.image('bikkuriman', 'assets/sprites/bikkuriman.png');

}

create() {

    this.add.sprite(200, 300, 'beball');
    this.add.sprite(500, 300, 'atari');
    this.add.sprite(800, 300, 'bikkuriman');

}
```

上記に対し、JSON付きのスプライトシートは、1つの画像ファイルの中にさまざまなスプライトをまとめて格納する。これだけでは1つの画像ファイルとしてしか扱えないので、座標を識別子であるスプライト名などとともに（場合によってはスプライトの大きさまで）記述したJSONを用いて、各スプライトの情報を管理する。この2つのファイルをセットで扱うことで、スプライトを個別にロードする必要がなくなり、スプライトの管理が楽になる。

そんなJSON付きスプライトシートは、上記の通り1つの画像ファイルとしては扱えないので`scene.load.image()`は使えない。

```javascript
preload() {
    this.load.atlas(assetName, pngPath, jsonPath);
}

create() {
    this.add.sprite(200, 200, assetName, spriteName);
}
```

JSON付きスプライトシートは、上記の通り`scene.load.atlas()`を使う。そして、スプライトシートを表示する場合は、`scene.add.sprite()`を利用する。ここで`assetName`はスプライトシートを区別するための文字列なので、わかりやすいものなら何でもいい。問題は`spriteName`の方で、これは**JSONファイル内に記述してあるスプライト名を指定する**必要がある。スプライトシート作成時に各スプライトには何らかの名前をつけているはずなので、それで指定する。場合によっては、このスプライト名が数字などになっている場合がある。その場合は名前を指定してスプライトシートを作成し直すか、JSONファイル内のスプライト名を直接書き換えてわかりやすいものにしておく。

### reference

1. [Multiple Sprites](https://phaser.io/examples/v3/view/game-objects/sprites/multiple-sprites)
1. [Phaser3の豆知識](https://qiita.com/gakuseikai/items/062d2c3d132bf430fcb9)
2. [Phaser3 でスペースを押したときにスプライトが落下するサンプル](https://hawksnowlog.blogspot.com/2018/04/fall-sprites-sample-phaser3.html)
