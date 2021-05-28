# 難読化について

## detail

元の機能はそのままに、コードを極端に読みにくくすることが難読化。コンパイル言語にはあまり馴染みがない（コンパイルして実行ファイルにするのだから、コードを難読化する意味はない。むしろ[リバースエンジニアリング](https://ja.wikipedia.org/wiki/%E3%83%AA%E3%83%90%E3%83%BC%E3%82%B9%E3%82%A8%E3%83%B3%E3%82%B8%E3%83%8B%E3%82%A2%E3%83%AA%E3%83%B3%E3%82%B0)を気にするべきではなかろうか）が、JavaScriptのようにユーザーの環境で動作するような言語には難読化の恩恵がある。

なお、SPAなどはJSファイルやCSSファイルをなどをまるっとコピーして自社のサーバーにて運用するなら、たとえどんな高度な難読化が施されていても関係がない。なぜなら、難読化は読みにくくするだけで機能はそのままだからだ。まぁデバッグがすこぶるやりにくくはなるだろうが。なので、そういう場合は[別の対策](https://qiita.com/kacchan6/items/d8576ab6b3c16cf670ca)を考えるべき。

大抵の場合、難読化はMinifyも兼ねているのでファイルサイズは減少するが、難読化の方法によってはファイルサイズは増加することがある。たとえば[こういう難読化](https://sanya.sweetduet.info/unyaencode/)とかな。いやおもしろいしこういうの好きだけどさ。

## reference

1. [難読化JavaScriptの作り方](https://tex2e.github.io/blog/javascript/obfuscation)
2. [ウェブアプリをソースごとパクる業者に対する対策](https://qiita.com/kacchan6/items/d8576ab6b3c16cf670ca)
3. [JavaScript難読化ツールの紹介と比較](https://kagasu.hatenablog.com/entry/2017/03/30/194024)
