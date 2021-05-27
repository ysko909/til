# TypeScriptでのクラス宣言と継承について

## detail

TypeScriptでのクラス宣言と継承について、挙動をメモする。

```typescript
class Hoge {
  x: number;
  y: number;
  
  constructor(x: number, y: number){
    this.x = x;
    this.y = y;
  }
  
  add(z: number){
    return new Hoge(this.x + z, this.y + z);
  }
}

let hoge: Hoge = new Hoge(1, 2);
console.log(hoge);
console.log(hoge.add(3));
console.log(hoge.constructor.name);

class Fuga extends Hoge {
  xx: string;
  yy: string;
  constructor(x: number, y:number, xx: string, yy: string){
    super(x, y);
    this.xx = xx;
    this.yy = yy;
  }
  add_fuga(z: number, zz: string){
    return new Fuga(this.x + z, this.y + z, this.xx + zz, this.yy + zz);
  }
  add_fuga_super(z: number){
    return super.add(z);
  }
  
}

let fuga: Fuga = new Fuga(10, 11, 'foo', 'bar');
let fuga2: Fuga = fuga.add(9)
let fuga3: Fuga = fuga.add_fuga(99, 'baz');
let fuga4: Fuga = fuga.add_fuga_super(123);
console.log(fuga);
console.log(fuga2);
console.log(fuga3);
console.log(fuga4);
console.log(fuga.constructor.name);
console.log(fuga2.constructor.name);
console.log(fuga3.constructor.name);
console.log(fuga4.constructor.name);

class Piyo extends Hoge {
  x : string;
  constructor(y: number){
    super('string x', y);
  }
  add(y: number){
    return new Piyo(y);
  }
  
}

let piyo:Piyo = new Piyo(2222);
let piyo2:Piyo = piyo.add(9999);
console.log(piyo);
console.log(piyo2);
console.log(piyo.constructor.name);
console.log(piyo2.constructor.name);

```

- TypeScriptでのクラス宣言は、`class`で宣言しプロパティを型情報とともに記述する。
- 継承する場合`extends`で継承元のクラスを指定する。このとき、コンストラクタが存在する場合は`super()`で親コンストラクタを呼び出さないといけない。`super()`を読みだした後で、必要な処理を追加できる。
- ちなみに、`.constructor.name`でクラス名を返してくれる。

```console
Object {
  x: 1,
  y: 2
}
Object {
  x: 4,
  y: 5
}
"Hoge"
Object {
  x: 10,
  xx: "foo",
  y: 11,
  yy: "bar"
}
Object {
  x: 19,
  y: 20
}
Object {
  x: 109,
  xx: "foobaz",
  y: 110,
  yy: "barbaz"
}
Object {
  x: 133,
  y: 134
}
"Fuga"
"Hoge"
"Fuga"
"Hoge"
Object {
  x: "string x",
  y: 2222
}
Object {
  x: "string x",
  y: 9999
}
"Piyo"
"Piyo"
```

- 親クラスのメンバ関数を呼び出す場合、`super.`構文で呼び出せる。なお、上記のコードでは`Fuga`クラスにおいて親クラス`Hoge`のメンバ関数`add()`を呼び出す場合、**処理内容や戻り値は親クラスに記述されている内容に準拠する**。よって、`supeer.add()`を呼び出す`add_fuga_super()`の戻り値は、`Fuga`クラスではなく`Hoge`クラスであることに注意。
- ちなみに上記のコードはCodepenのTypeScript環境で実行している。`add_fuga_super()`の戻り値が`Hoge`クラスであるにもかかわらず、変数宣言時に`Fuga`クラスであると宣言しているが、これはエラーにならない。そういうものなのか・・・（`Fuga`を`Hoge`や`Piyo`に書き換えてもエラーとかにはならなかったので、少なくともCodepenのTS環境はこういうものらしい）。

```typescript
class GameScene extends Phaser.Scene {
    constructor(sceneName) {
        super(sceneName);
    }

    preload() {

    }

    create() {
      this.add.text(100, 100, 'hoge');

    }
}

class MainScene extends GameScene {
    constructor() {
        super('mainScene');
    }

    preload() {

    }

    create() {
      super.create(); // `super.method()`とすると基底クラスのメソッドを実行する
      this.add.text(200, 200, 'fuga');
    }
}

let mainScene = new MainScene();

let config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    backgroundColor: '#e0e0e0'
};

let game = new Phaser.Game(config);

game.scene.add('mainScene', mainScene);

game.scene.start('mainScene');
```

- `extends`した派生クラス内で、基底クラスのpublicメソッドを利用する場合は`super.method()`と記述すると実行できる。
- 上記の例では、基底クラス側で「hoge」という文字列を、派生クラス側で「fuga」という文字列を表示している。`super.create()`を実行しない場合、「fuga」のみが画面に表示される。これは、基底クラスにあったpublicメソッドである`create()`を、派生クラス側でオーバーライドしているため。
- [オーバーライド](https://en.wikipedia.org/wiki/Method_overriding)は乱暴に要約すると、メソッド名や引数はそのままに、派生クラス側で処理内容を上書きしてしまうこと。ここでは、基底クラスの`create()`メソッドが「hoge」を表示するという処理であるのに対し、派生クラスの`create()`は同じメソッド名でありながら「fuga」を表示する処理に置き換わっている。そのままだと基底クラス側の`create()`は実行されない。オーバーライドされる前のメソッドを実行するためには、上記の通り`super.create()`と記述して、**基底クラス側の**`create()`を実行するよと明示する必要がある。

## reference

1. [クラス](https://typescript-jp.gitbook.io/deep-dive/future-javascript/classes)
1. [クラス](https://docs.solab.jp/typescript/class/inheritance/)
1. [JSのクラス名をゲットする](https://qiita.com/makerbox/items/3f6ed31abbb128f5edb1)
1. [JavaScriptの「型」の判定について](https://qiita.com/south37/items/c8d20a069fcbfe4fce85)
