# TIL - JavaScript

JSに関することはここに書く。

## JavaScriptでのアップロード機能の実装

### detail

- JavaScriptでローカルの画像をアップロードし、canvas上に表示するプログラムを作成。HTMLとJavaScriptなので、PlayCode上にて仮実装。
- [参考元URL](https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html)では、画像ファイルであれば受け付けていたが、ここではPNGのみを対象とした。`type.match()`の対象を、`image/png`とするとPNGに限定できる。
- もともとの仕様では画像がアップロードされた場合のみcanvasの更新を行っていたため、対象外のファイルがアップロードされた場合は、過去にアップロードされたデータや子要素が画面に残り続ける。それを解消するため、PNG以外がアップロードされた場合は一度canvasを削除する仕様とした。また、同様にダウンロード用URLが存在する場合は子要素から削除する。
- PlayCodeは特定のボタンを押すと、ソースのフォーマットを行ってくれる。その際、アロー関数に引数がある場合は**かっこを外す**（引数がない場合はかっこを省略できないので、そのまま残る）。「挙動は一緒なんだから、文字は少ない方がいいでしょ」という判断なのかもしれないが、個人的にはまだアロー関数に慣れてないので、あんまり省略されると混乱する。
- アロー関数ではかっこだけでなく、1行で記述する場合は波かっこまで省略可能なので、ぱっと見の可読性に難があって割と混乱しがち。
- どっちがいいかはともかくとして、「どっちでも読める」程度には慣れたい。

### reference

1. [File APIとCanvasでローカルの画像をアップロード→加工→ダウンロードする](https://www.tam-tam.co.jp/tipsnote/javascript/post13538.html)
1. [ソースのgist](https://gist.github.com/ysko909/0f14a71c5604ded658e3a4f8fb837e6d)

## JavaScriptで、p5.jsを使ったボタンの生成方法

### detail

- p5.jsによるボタンの生成。`createButton()`を使うと、動的にボタンを生成できる。

```javascript
let button;

function setup() {
    createCanvas(400, 300);
    button = createButton('hoge');
    background(50);
}

function draw() {

}
```

- 上記のコードでは、実行するとCanvas外にボタンが1つ生成される。なお、これだけだと押してもボタンに挙動が設定されていないため、動作はしない。

```javascript
let button;

const drawRect = () => {
  fill(random(255));
  rect(random() * width, random() * height, random(width), random(height));
}

function setup(){
  createCanvas(400, 300);
  
  button = createButton('draw rectangle');
  button.mousePressed(drawRect);
}

function draw(){
  
}

```

- ボタンに挙動を設定するには、生成したボタンにアクションすべきイベントと挙動を指定する必要がある。上記のコードだと、`button.mousePressed(drawRect);`がその部分に当たる。

```javascript
let buttonDraw, buttonDel;

const drawRect = () => {
  fill(random(255));
  rect(random() * width, random() * height, random(width), random(height));
}

const clearCanvas = () => {
  background(255);
}

function setup(){
  createCanvas(400, 300);
  background(255);
  
  buttonDraw = createButton('draw rectangle');
  buttonDraw.mousePressed(drawRect);
  
  buttonDel = createButton('delete');
  buttonDel.mouseOver(clearCanvas);
}

function draw(){
  
}

```

- ボタンは複数生成が可能で、もちろんそれぞれについてアクションすべきイベントや機能を個別に設定できる。
- HTMLに書かなくてもJS側で動的に生成できるのは、編集する資産を一本化できるので便利。
- ボタン以外にもスライダーなんかも同じように生成できるので、基本的にJS側でオブジェクトを生成するように記述して、HTMLと往復しなくてもいいようなコーディングスタイルにしよう。

### reference

1. [12_1：コントロール要素 Creative Coding p5.js](https://himco.jp/2019/03/25/12_1%EF%BC%9A%E3%82%B3%E3%83%B3%E3%83%88%E3%83%AD%E3%83%BC%E3%83%AB%E8%A6%81%E7%B4%A0-creative-coding-javascrip/)

## p5.jsの基礎

### detail

p5.jsの環境はCodepenでp5.jsのライブラリを参照する設定すればいい。

- p5.jsの基本中の基本。

```javascript
const W = 800;
const H = 600;

function setup(){
  createCanvas(W, H);
  
  background(255);
}

function draw(){
  ellipse(60, 60, width / 10, height / 10);
}
```

- p5.jsは`setup()`が1度だけ実行され、次に`draw()`が繰り返し実行されるという状態の遷移で動作する。
- `setup()`はその名前の通り、初期設定などで用いる。そして`setup()`内で宣言された変数は、他の関数からはアクセスできないとある。まぁ、少なくとも`let`とか使ってればJSの仕様上大丈夫だろう。
- `draw()`は自動的に繰り返し実行される。初期設定以外の実際の描画処理はこの関数の中に記述する。
- 上記の場合、`setup()`でCanvasが生成される。Canvasのサイズは`const`で宣言した。その後、Canvasを指定した背景色で塗りつぶす。0が真っ黒、255が真っ白。要は色指定でよくある、RGBの値で指定すればいい。
- `background()`は背景に利用する色を設定する。デフォルトは**透明**。
- 実際の描画処理は`draw()`で実行される。ここでは`ellipse()`を実行して円を描画している。
- `ellipse(x座標, y座標, 横幅, 高さ)`で描画する円を設定する。
- `draw()`関数の中で`width`と記述すると、Canvasの横のサイズを取得できる。同様に`height`と記述すると、Canvasの高さを取得できる。ここでは取得した縦横の10分の1のサイズの楕円を描画した。
- 上記のコードで一応簡単だがp5.jsの挙動がわかると思う。ただし、現状のコードでは**ちょっとした問題がある**。

```javascript
const W = 800;
const H = 600;
let position = 0;

function setup(){
  createCanvas(W, H);
  
  background(255);
}

function draw(){
  
  ellipse(60 + position, 60 + position, width / 10, height / 10);
  
  position += 2;
}
```

- 今度のコードは、楕円を描画する位置を少しずつズラしている。
- 実行してみるとわかるが、**1度描画された楕円がその後もCanvas上に残り続ける**。これが目的の描画なら問題ないが、「楕円が斜めに**移動する**」挙動を実現したい場合は、過去に描画されたシェイプは残らない方が望ましい。その仕様を満たすには、**Canvasの内容をクリア**する必要がある。

```javascript
const W = 800;
const H = 600;
let position = 0;

function setup(){
  createCanvas(W, H);
}

function draw(){
  background(255);
  
  ellipse(60 + position, 60 + position, width / 10, height / 10);
  
  position += 2;
}
```

- 背景色を設定する`background()`関数を、`setup()`関数から`draw()`関数の冒頭に移動した。これにより、`draw()`関数が実行されると、最初に**Canvasの内容が設定された色でリセットされる**。その後、楕円が描画される。この挙動により、楕円が斜めに移動しているように見える。
- `background()`関数は多くの場合、`draw()`関数の冒頭でCanvasの内容をリセットするのに用いられる。`setup()`関数内で利用される場合は、背景色の設定が1度でいいか、アニメーションの開始フレームの背景色を設定する場合に利用される。
- デフォルトのカラー空間は、各値が0から255の範囲のRGB。HSLでも指定が可能らしい。
- 第2引数はアルファ値で、省略可能。アルファの範囲はデフォルトで0から255。

## マウスに追随するシェイプをp5.jsで実装する

### detail

```javascript
const W = 800;
const H = 600;

function setup(){
  createCanvas(W, H);
}

function draw(){
  ellipse(mouseX, mouseY, width / 10, height / 10);  
}
```

- マウスカーソルを適当に動かすと、カーソルに合わせて楕円が描画される。ゴリゴリ動かすとどんどん楕円が描画される。
- `mouseX`と`mouseY`でマウスカーソルのX座標とY座標を取得できる。PCのマウス入力でなくスマートフォンなどのタッチ入力である場合は、**直近にタッチされた位置のX座標およびY座標を保持**する。

```javascript
  console.log('mouseX: ' + mouseX);
  console.log('mouseY: ' + mouseY);
```

- 上記のコードを記述することで、`mouseX`と`mouseY`の座標を出力できる。見るとわかるが、Canvasの左上を`(0, 0)`とした値で出力される。
- `draw()`関数内に`background()`がないので、シェイプが残り続けるのはさっきと同様。

```javascript
const W = 800;
const H = 600;

function setup(){
  createCanvas(W, H);
}

function draw(){
  if (mouseIsPressed) {
    fill(0);
  }
  else{
    fill(255);
  }
  
  ellipse(mouseX, mouseY, width / 10, height / 10);
  
}
```

- マウスがクリックされているか否かを判断するのは、`mouseIsPressed`を参照する。Trueであればクリック状態にある。
- `fill()`関数はシェイプの塗りつぶす色を設定する。1度設定すると、以降に描画されるすべてのシェイプに影響する。色の設定はデフォルトのカラー空間だと、0から255の範囲のRGB。引数1つで設定する場合はグレースケールになる。
- グレースケール指定の第2引数、カラー設定の第4引数はアルファ値で省略可能。アルファの範囲も、他と同様デフォルトで0から255。

### reference

1. [p5.js Get Started](https://p5js.org/get-started/)
1. [p5.js スピード入門](https://himco.jp/category/p5-js-%e3%82%b9%e3%83%94%e3%83%bc%e3%83%89-%e5%85%a5%e9%96%80/)

