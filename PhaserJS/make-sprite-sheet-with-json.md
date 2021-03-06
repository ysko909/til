## Phaser3用のスプライトシートをJSON付きで作成する

### detail

#### スプライトシートとは

プレイヤーや敵、NPCなどのキャラクターは、モーションを「スプライトシート」というファイルで管理する。スプライトシートとは、コマ割りして描いたモーションの断片をまとめた画像ファイル。このファイルが持つ各モーションの断片を「スプライト」というが、スプライトを短いスパンで同じ場所に連続表示すると、パラパラ漫画と同じ要領で「動いて見える」というわけ。

![pic](2021-02-21-09-19-40.png)

上記は右方向に歩くモーションを個別に描いてスプライトとし、1つの画像ファイルとしてまとめたもの。これで1つのスプライトシートとなる。もちろん、右方向以外のモーションが描かれていないのでそれらは別途描いてスプライトシートとしてまとめる。問題は描いたスプライトをどうやってまとめて、どうやってゲームエンジン（ここではPhaser3）で利用するか、ということ。

ちなみに当たり前だが、余白部分は透過する必要があるので拡張子はPNGになる。

#### スプライトシートの作り方

スプライトシートはスプライトをまとめたもの。なので、個別の画像を集約して1つの大きな画像にするわけで、普段使っているようなドローソフトで作成自体は可能。

ただし、スプライトシートだけ作った場合は、**単純にスプライトをならべただけの画像ファイル**のなので肝心の「どこの座標にどんなスプライトが描かれているか」という情報がない。プログラミングする段階で座標を都度手動で設定するなら問題ないが、スプライトシートが多くなってくると「ただ座標を延々と設定する作業」が非常に億劫になる。なので、基本的には単純にスプライトシートを作るだけでなく、スプライトの情報が格納されたJSONファイルを同時に作る必要がある。JSONファイルはさすがにドローソフトではどうにもならないので、スプライトシートを作るためのソフトウェアが必要になる。

JSONがエクスポートできるような、スプライトシートの作成方法はいくつか方法がある。

1. [TexturePacker](https://www.codeandweb.com/texturepacker)
   - 多分一番メジャー。
   - 画像の最適化で容量をごっそり削れるので、コンパクトにまとめたいときなどに便利。
   - Phaser3だけでなく、[Cocos2d](https://www.cocos.com/en/)などのさまざまなゲームエンジンに最適化されたファイル形式でエクスポートできる。
   - マルチパックなどいろんな機能もあるのだけど、それらの高機能は**有償版のみ**利用可能。無償版も一応使えるのだがただパッケージ化するだけなので、それだけのためにわざわざインストールするのは面倒。
2. [Free Sprite Sheet Packer](https://www.codeandweb.com/free-sprite-sheet-packer)
   - 上記TexturePackerの無償機能部分だけで構成されたwebApp。
   - TexturePackerをわざわざインストールして無償版で利用するくらいなら、こっちを利用したほうがローカル環境を汚さなくて済むので良い。
3. [Free Tex Packer](https://free-tex-packer.com/app/)
   - インストール不要のwebApp。デスクトップ版もあるが、web版でも欲しい機能はちゃんと実装している。
   - [OSS](https://github.com/odrick/free-tex-packer)なので、何だったらコントリビュートすることも可能。
   - 素晴らしいのは上記TexturePackerの無償版機能にプラスして、各ゲームエンジンに最適化されたJSONをエクスポートできる機能が備わっていること。
   - さらに[TinyPNG](https://tinypng.com/)のデベロッパーキーを持っている場合はTinifyしてロスレス圧縮が可能と、まさに至れり尽くせり。

というわけで、この中ではFree Tex Packerを使うのが正義となる。まぁwebサービスなのでいつなくなるか・・・という不安もあるが。

Free Tex Packerの使い方はUIを見ればわかるので、ここでは詳細に解説はしない。

注意点としては下記の通り。

- エクスポートする際にFormatを「Phaser3」にしておくこと。Phaser3に最適なJSONを出力してくれる。
- Texture Name、File Nameはわかりやすいものに変更しておく。変更しないと、インポートした元画像のファイル名を利用する。
- widthやheightの出力画像サイズを変更する。スプライトの数にも依存するが、ひたすら水平方向あるいは垂直方向に対し、1列にダラダラとスプライトが並んでいるのは見通しが良くない。
- Allow trimはPNGの透過部分を削って、スプライトシートのサイズをなるべく減らすようにスプライトを配置してくれる。スプライトシートが巨大で**なるべく軽量化したいときに使う**。座標はJSONで出力されるので、インポートする際のファイル名管理などがしっかりできていれば問題ないはず。

### reference

1. [Free Tex Packer](http://free-tex-packer.com/)
2. [Pack Sprite sheets for games and sites for free!](https://phaser.io/news/2020/02/free-texture-packer)

