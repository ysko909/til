## 音声素材やBGMをロードして再生する

### detail

ボタンを押したときの効果音やBGMなどは、音声のアセットとして同じようなコードで実装が可能。

```typescript
preload() {
    this.load.audio('whiteNoise', 'whiteNoise.mp3');
    this.load.audio('buttonSound', './assets/sound/button/button_ok.mp3');
}

create(){
    let buttonSound = this.sound.add('buttonSound');
    
    let whiteNoise = this.sound.add('whiteNoise', { loop: true });
    whiteNoise.play();

    const { width, height } = this.game.canvas;

    const zone = this.add.zone(width / 2, height / 2, width, height);

    zone.setInteractive({
        useHandCursor: true
    });

    zone.on('pointerdown', () => {
        buttonSound.play();
    });

}
```

まず`preload()`内で、`scene.load.audio()`を使い音声ファイルをロードする。次に`create()`内で`scene.sound.add()`を実行し、シーンに音声ファイルを追加する。あとは再生したいタイミングのコード部分で`play()`を実行すればいい。

`loop`プロパティをtrueにするとループ再生される。

Phaser3自身が対応している再生可能な音声ファイルは`*.wav`や`*.mp3`、`*.ogg`がある。ただ、`*.ogg`はブラウザ側が対応していない事が多く、Safariは未対応だったりする。そのため、基本的にはファイルサイズ的にも`*.mp3`を利用することになると思う。ただし、[MP3はエンコード時に無音部分が必ず挿入されるため完全なループ素材とすることが難しい](https://ameblo.jp/djdaishizen/entry-11996902609.html)せいもあり、完璧なループ素材としたい場合は`*.wav`一択になる。

### reference

1. [Audio](https://rexrainbow.github.io/phaser3-rex-notes/docs/site/audio/)
2. [mp3に変換すると無音部分ができてしまう理由](https://ameblo.jp/djdaishizen/entry-11996902609.html)
