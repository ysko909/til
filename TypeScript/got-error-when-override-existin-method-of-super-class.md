# 'foo' is defined as a property in class 'A', but is overridden here in 'B' as an accessor」と言われたら基底クラスのメソッドをオーバーライドしてるぞ気をつけろ

## detail

長ったらしいエラーメッセージだが、要は「`foo`ってAクラスにプロパティとしてもともと定義されてるけど、Bクラス（派生クラス）で同名のアクセサとしてオーバーライドしてるぞ。いいのか？ダメだろう」っていう意味合いのエラー。

ちなみに自分が遭遇したのは、PhaserJSを使ったコードを書いていたとき。Phaserが持っているクラスを基底クラスとし、派生クラスを定義した。

```typescript
export class Dialog extends Phaser.GameObjects.Container {
    private _x: number;
    private _y: number;
    private _width: number;
    private _height: number;

    private _g: Phaser.GameObjects.Graphics;
    private _r: Phaser.GameObjects.Graphics;

    private _strokePositionMargin: number = 10;

    constructor(scene: Phaser.Scene, position: { x: number; y: number; width: number; height: number; }) {
        super(scene)

        // 中略
    }

    // アクセサ
    get x(): number { // ここでエラー
        return this._x;
    }

    get y(): number { // ここでもエラー
        return this._y;
    }

    get width(): number { // ここでもエラー
        return this._width;
    }

    get height(): number { // ここでもエラー
        return this._height;
    }

}
```

上記のように、4箇所でエラーがそれぞれ発生しているが、どれもエラーメッセージとしては同じ内容。

```console
'x' is defined as a property in class 'Container', but is overridden here in 'Dialog' as an accessor.
'y' is defined as a property in class 'Container', but is overridden here in 'Dialog' as an accessor.
'width' is defined as a property in class 'Container', but is overridden here in 'Dialog' as an accessor.
'height' is defined as a property in class 'Container', but is overridden here in 'Dialog' as an accessor.
```

上記のように、アクセサを定義している部分でエラーが発生している。これらのアクセサは、基底クラスに存在するメンバのアクセサとして機能しているものをオーバーライドしてしまっている。

不味いのは、これらの新規で作ってオーバーライドした側のアクセサが返すプロパティと、オーバーライドされた側のアクセサが返すプロパティが異なる点だ。オーバーライドした側は新規で宣言したプロパティの`_x`などを返すが、オーバーライドされた側が返すのは基底クラスの`x`。見た目が一緒でも返す対象が異なるため、バグの原因になりかねない。

できるなら、新規で宣言するプロパティは基底クラスに存在しないような名前にしておくべき。仮にアンダースコアをつけて宣言したとしても、アクセサはアンダースコアを取って宣言することがあり、プロパティ名そのものでオーバーライドしなくてもアクセサ側でオーバーライドしてしまう可能性がある。

## reference

1. [TS2611: 'foo' is defined as a property in class 'A', but is overridden here in 'B' as an accessor](https://stackoverflow.com/questions/63750710/ts2611-foo-is-defined-as-a-property-in-class-a-but-is-overridden-here-in)
2. [Phaser.GameObjects.Container](https://photonstorm.github.io/phaser3-docs/Phaser.GameObjects.Container.html)
