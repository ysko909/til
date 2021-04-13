# TIL - p5.js

p5.jsに関することはここに書く。

## p5.jsの基礎

### detail

p5.jsの環境は、一番手軽な調達方法だとCodepenでp5.jsのライブラリを参照する設定すればいい。

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

## p5.jsでのシェイプ描画処理について

### detail

- 線の描画は`line()`関数を利用する。

```javascript
W = 800;
H = 600;

function setup(){
  createCanvas(W, H);
}

function draw(){
  background(255);
  
  line(20, 20, 100, 100);
  line(110, 110, 200, 200);
  
}
```

`line()`関数は引数に「始点のX座標、始点のY座標、終点のX座標、終点のY座標」を指定する。`strokeWeight()`で任意の数字を指定することで、描画する線の太さを指定できる。デフォルトの太さは1。

```javascript
  line(20, 20, 100, 100);
  strokeWeight(10);
  line(110, 110, 200, 200);
```

線の太さ設定は`strokeWeight()`で数字を指定すればいいが、上記のような書き方をすると最初の`line()`関数で描画した線も含めて**すべてのシェイプが太さ10に設定されてしまう**。

```javascript
  strokeWeight(1);
  line(20, 20, 100, 100);
  strokeWeight(10);
  line(110, 110, 200, 200);
```

各シェイプで異なる太さを設定したい場合は、**シェイプ描画の直前で太さを明記しておく**（たとえデフォルトの1であっても）ことが重要。

- 四角の描画は`quad()`関数を利用する。ここで言う「四角」とは正方形や長方形とは異なり、角の部分は直角じゃなくても構わない。なお、これ以降は`setup()`関数や`draw()`関数の一部を省略する（線の描画のときと同じコードなので）。

```javascript
  fill(255, 0, 0);
  strokeWeight(2);
  quad(200, 20, 300, 30, 310, 100, 210, 110);
```

`quad()`関数は「x1, y1, x2, y2, x3, y3, x4, y4」でそれぞれ座標を指定する。指定された極座標で四角を描画するため、指定の座標によってはひねったような形の四角（三角を2つ並べたような形）を描画することも可能。

- 三角の描画は`triangle()`関数を利用する。

```javascript
//draw triangle
  fill(0, 255, 0);
  triangle(380, 20, 440, 100, 320, 100);
```

`triangle()`関数は「x1, y1, x2, y2, x3, y3」で座標を指定する。

- 長方形の描画は`rect()`関数を利用する。

```javascript
  fill(0, 0, 255);
  rect(460, 20, 100, 100);
  
  fill(20, 200, 100);
  rect(610, 20, 100, 100, 15);
```

`rect()`関数は他のシェイプを描画する関数と異なっていて、引数が「左上のx座標、左上のy座標、幅、高さ」になっている。角の座標を個別に指定しなくていいので、単純に正方形または長方形を描画するなら`rect()`関数を利用したほうが楽。第5引数以降は、角に丸みをつけるのに利用する。左上、右上、右下、左下の順でそれぞれの角の丸みを指定できる。途中で省略した場合は、直前で指定された値を引き継ぐ。上記の例だと、左上の丸みを15に指定しているので、他の角も同様に15で指定したことになる。

### reference

1. [Pythonのlambda（ラムダ式、無名関数）の使い方](https://note.nkmk.me/python-lambda-usage/)
1. [2_3：p5.js 線、四角形、三角形、長方形を描く](https://himco.jp/2019/02/08/2_3%ef%bc%9ap5-js-%e7%b7%9a%e3%80%81%e5%9b%9b%e8%a7%92%e5%bd%a2%e3%80%81%e4%b8%89%e8%a7%92%e5%bd%a2%e3%80%81%e9%95%b7%e6%96%b9%e5%bd%a2%e3%82%92%e6%8f%8f%e3%81%8f/)

## p5.jsのシステム変数について

### detail

システム変数とは、ここでは「p5.jsが独自かつ自動的に値を設定・保持する変数」のことを指す。システム変数と同じ名前の変数をプログラマーが作成してはいけない。

```javascript
const W = 800;
const H = 600;

function setup(){
  createCanvas(W, H);
}

function draw(){
  background(255);
  
  const TEXT_SIZE = 30;
  textSize(TEXT_SIZE);
  fill(0);
  text('width: ' + width, 10, TEXT_SIZE);
  text('height: ' + height, 10, TEXT_SIZE * 2);
  text('mouseX: ' + mouseX, 10, TEXT_SIZE * 3);
  text('mouseY: ' + mouseY, 10, TEXT_SIZE * 4);
  text('frameCount: ' + frameCount, 10, TEXT_SIZE * 5);
  text('deltaTime: ' + deltaTime, 10, TEXT_SIZE * 6);
  text('focused: ' + focused, 10, TEXT_SIZE * 7);
  text('windowWidth: ' + windowWidth, 10, TEXT_SIZE * 8);
  text('windowHeight: ' + windowHeight, 10, TEXT_SIZE * 9);
  text('getURL(): ' + getURL(), 10, TEXT_SIZE * 10);
  text('movedX: ' + movedX, 10, TEXT_SIZE * 11);
  text('movedY: ' + movedY, 10, TEXT_SIZE * 12);
  text('mosueButton: ' + mouseButton, 10, TEXT_SIZE * 13);
  text('mouseIsPressed: ' + mouseIsPressed, 10, TEXT_SIZE * 14);
  text('touched: ' + touches, 10, TEXT_SIZE * 15);
  
    
}
```

- `width`：Canvasの幅。`createCanvas()`で指定した幅が格納され、デフォルトは100。
- `height`：Canvasの高さ。`createCanvas()`で指定した高さが格納され、デフォルトは100。
- `mouseX`：マウスカーソルの水平方向の座標。
- `mouseY`：マウスカーソルの垂直方向の座標。
- `frameCount`：プログラム開始以降、表示されたフレームの数が格納される。FPSが高いほど、早く増加する。
- `deltaTime`：n番目のフレームとn+1番目のフレームの間の時間差が、ミリ秒単位で格納されている。
- `forcusd`：p5.jsの動作しているウィンドウがアクティブかどうか。
- `windowWidth`：p5.jsが動作しているウィンドウ自体の幅。Canvasのサイズとは別。
- `windowHeight`：p5.jsが動作しているウィンドウ自体の高さ。Canvasのサイズとは別。
- `getURL`：p5.jsが動作しているURLを取得する。これは正確に言うとシステム変数ではなく関数。
- `movedX`：マウスカーソルが移動したとき、水平方向の移動量を出力する。移動していなければ0。
- `movedY`：マウスカーソルが移動したとき、垂直方向の移動量を出力する。移動していなければ0。
- `mouseButton`：マウスのボタンが押されたとき、LEFT・CENTER・RIGHTのいずれかを出力する。ちなみに、ボタンから指を離してもここの表示は変わらない。これは「最後に押されたボタン」を保持し続けるため。そのため、初期状態だと空っぽ。一度でもボタンが押されたら、その情報はそのまま保持される。
- `mouseIsPressed`：マウスのボタンが押されているか否かを出力する。押されていなければFalse、押し続けている間はずっとTrueになる。
- `touched`：スマートフォンやタブレットのように、マウスポインタでなくタッチ操作するようなデバイスはこの配列に格納された座標を利用する。

**リンク**:

1. [P5.js 日本語リファレンス](https://qiita.com/bit0101/items/91818244dc26c767a0fe)
1. [3_1：p5.js 変数](https://himco.jp/2019/02/09/3_1%ef%bc%9a%e5%a4%89%e6%95%b0/)

## p5.jsを使ってDOM要素の追加を行う方法

### detail

任意のDOM要素を追加する場合、`createElement()`を実行する。DOM要素を削除する場合、`remove()`を実行する。

```javascript
let hoge;

function setup(){
  createCanvas(400, 200);
}

function draw(){
  background(255);
  
  text(frameCount, 100, 100);
  
  if (frameCount === 200) {
    hoge = createElement('h2', 'hoge');
    hoge.id('hoge');
  }
  
  if (frameCount === 400){
    hoge.remove();
    frameCount = 0;
  }
  
}
```

上記のコードだと、フレームカウントに応じて要素を追加したり削除したりしている。

なお、p5.jsのコードは最初、`draw()`関数内で変数`hoge`を宣言していた。このロジックだと**要素の削除ができない**。追加はできるので、h2要素がただひたすら追加されてしまう。

```javascript
function draw(){
  let hoge; // コレ

  background(255);
  
  text(frameCount, 100, 100);
  
  if (frameCount === 200) {
    hoge = createElement('h2', 'hoge');
    hoge.id('hoge');
  }
  
  if (frameCount === 400){
    hoge.remove();
    frameCount = 0;
  }
  
}
```

原因は、`draw()`が繰り返し呼ばれるせい。`let`を使ってブロックスコープにしていた変数`hoge`は、`draw()`の実行が終了すると同時に**格納していた内容とともに揮発してしまう**。フレームカウントごとに要素を追加したり削除したりするため、フレームカウント200の時点では変数`hoge`に要素の情報が格納されていたとしても、その実行が終われば要素の情報を含んだ変数が揮発してしまう。次のフレームカウント201において`draw()`が実行された時点で、変数`hoge`が**新たに宣言される**ため内容が`undefined`になる。この挙動は以降も同様なので、フレームカウント400の時点では変数`hoge`が宣言されていても、とっくに**要素の情報が揮発してしまっている**。よって、`undefined`では`remove()`メソッドをもっていないのでエラーになり、要素の削除ができない。というわけで、この問題を解消するには変数を`draw()`の外で宣言するのが一番単純な解決方法になる。

### reference

1.[特になし]()

## p5.jsでブラー効果を実装する方法

### detail

p5.jsでオブジェクトにブラー効果を付与する。

```javascript
const W = 800;
const H = 600;

let ffCount = 30;
let ff = [];

function FfObj(){
  this.pX = random(0, width);
  this.pY = random(0, height);
  this.noiseX = random() * 1000;
  this.noiseY = random() * 1000;
  this.noiseScale = random(0.001, 0.02);
}

FfObj.prototype.updateMe = function() {
    this.pX += noise(this.noiseX)*4-1.86;
    this.pY += noise(this.noiseY)*4-1.86;
    if (this.pX < 0) { this.pX = 0;}
    if (this.pX > width) { this.pX = width;}
    if (this.pY < 0) { this.pY = 0;}
    if (this.pY > height) { this.pY = height}
    this.noiseX += this.noiseScale;
    this.noiseY += this.noiseScale;
}
 
FfObj.prototype.drawMe = function() {
    fill(255, 255, 200, 255);
    ellipse(this.pX, this.pY, 10);
}

function setup(){
  createCanvas(W, H);
  background(0);
  noStroke();
  
  for (let i=0; i < ffCount; i++){
    ff[i] = new FfObj();
  }
}

function draw(){
  // background(0);
  
  fill(0, 20);
  rect(0, 0, width, height);

  for (let i=0; i<ffCount; i++) {
    ff[i].drawMe();
    ff[i].updateMe();
  }
}
```

### reference

1. [p5.jsで「ブラー」や「グロー」を使う方法](https://infosmith.biz/blog/it/p5js-blur-glow)

## p5.jsをHSBで使う方法

### detail

p5.jsのデフォルトのカラーモードはRGBだが、`colorMode()`を実行することでHSBに変更できる。

```javascript
function setup(){
  createCanvas(600, 600);
  colorMode(HSB, height, height, height, 100);
  background(0);
  
}

function draw(){
  fill(0, 10);
  rect(0, 0, width, height);
  
  fill(mouseX, mouseY, height);
  rect(mouseX, mouseY, 100, 100);
  
}
```

マウスポインタに四角形が追随する。その際、マウスポインタの座標によって四角の色が変化する。四角は軌跡を伴って移動する。この軌跡は、`draw()`関数内でAlpha（透明度）を10に設定した黒の四角を、描画したマウスポインタの四角の上からかぶせることで実装している。

`colorMode()`でHSBに変更する場合、第1引数にHSBを指定すればいい。第2引数以降はHue、Saturation、Brightness、Alphaにそれぞれ上限値をセットできる。設定しなかった場合、`colorMode(HSB, 360, 100, 100, 1)`が実行される。デフォルトの場合、Alphaが小数である点は注意が必要。まぁ、自力で設定しちゃえばいいじゃん、って話なんだが。

### reference

1. [カラー(Color)](https://himco.jp/2019/09/08/%EF%BC%97%EF%BC%9A%E3%82%AB%E3%83%A9%E3%83%BCcolor/)
