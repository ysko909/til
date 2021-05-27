# `Class 'classB' incorrectly extends base class 'classA'. Property 'hoge' is private in type 'classA' but not in type 'classB'.`と言われたらスーパークラスのプライベートなプロパティを再定義してしまっている

## detail

```typescript
class classA {
    private hoge=1;

    constructor(){
        
    }
}

class classB extends classA {
    public hoge=2; // publicがなくても一緒

    constructor(){
        super();
    }
}
```

上記のようなコードを書くとエラーになる。

`Class 'classB' incorrectly extends base class 'classA'. Property 'hoge' is private in type 'classA' but not in type 'classB'.`

このエラーは「`hoge`っていうプロパティはスーパークラスのプライベートだぞ、サブクラスのじゃないぞ（だから再定義できないぞ）」と言っている。

```typescript
class classA {
    private hoge=1;

    constructor(){
        
    }
}

class classB extends classA {
    private hoge=2; // publicをprivateにした

    constructor(){
        super();
    }
}
```

サブクラスのプロパティを`private`に変更すると、エラーになるのは変わらないがメッセージが上記とは異なる。

`Class 'classB' incorrectly extends base class 'classA'. Types have separate declarations of a private property 'hoge'.`

こっちは、「同じ`hoge`って名前で、スーパークラスとサブクラスで別々に宣言してるぞ（名前変えるか何とかしろよ）」というエラー。

```typescript
class classA {
    public hoge=1;

    constructor(){
        
    }
}

class classB extends classA {
    private hoge=2;

    constructor(){
        super();
    }   
}
```

逆に、スーパークラスで`public`、継承先で`private`にした場合はエラーメッセージが少し異なる。

`Class 'classB' incorrectly extends base class 'classA'. Property 'hoge' is private in type 'classB' but not in type 'classA'.`

「`hoge`っていうプロパティは**サブクラスのプライベート**だぞ、スーパークラスじゃないぞ」と、さっきの逆のことを言っている。ただ、どちらのパターンも継承元か継承先でプロパティが重複し、アクセス修飾子が異なるプロパティに対して再定義を行うと出現するエラー。

対処法はサブクラス側のプロパティ名を、スーパークラスとは異なる名前にすればいい。

```typescript
class classA {
    private hoge=1;

    constructor(){
        
    }
}

class classB extends classA {
    private hogehoge=2; // 名前を変更

    constructor(){
        super();
    }
}
```

単純にサブクラス側のプロパティ名を変更しただけだが、これだけでもエラーの解消は可能。

## reference

1. [クラス](https://nju33.com/notes/typescript/articles/%E3%82%AF%E3%83%A9%E3%82%B9)
