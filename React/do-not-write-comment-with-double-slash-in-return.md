# Reactでは`return`内に`//`でコメントを書いてはいけない

## detail

何を言っているのかと思われるだろうが、なんでもReactで`return`内に`//`を使ってコメントを書くとエラーを吐くらしい。そんな馬鹿な。

```jsx
import React, { Component } from 'react';
import { render } from 'react-dom';

class App extends Component {
  render() {
    console.log('App started');

    return <h1>hoge</h1>; // foo
  }
}

render(<App />, document.querySelector('#app'));
```

上記の場合はOK。

```jsx
import React, { Component } from 'react';
import { render } from 'react-dom';

class App extends Component {
  render() {
    console.log('App started');

    return (
      // hoge
      <h1>hoge</h1>
    );
  }
}

render(<App />, document.querySelector('#app'));
```

上記の場合はダメらしい。

解決策は`//`を使ったコメントをしなければいいらしい。

```jsx
return (
  /* hoge */
  <h1>hoge</h1>
);
```

とかはOK。

```jsx
const elem = (
  /* hoge */
  <h1>hoge</h1>
);

return elem;
```

あとは、変数宣言しちゃってその変数を`return`する分には問題ないようだ。

ちなみに、この現象はビルドの難読化を起因に発生するため、ネット上にあるReactのPlayground環境では再現しない。

## reference

1. [Minified React error #152 - comments before JSX](https://github.com/facebook/create-react-app/issues/8687)
1. [[React] 本番環境でのみ画面が真っ白になる場合](https://qiita.com/siruku6/items/5744c1e3362fb31f8e1c)
