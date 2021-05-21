## アセット名が重複してしまった場合は先に読み込んだ方が優先される

### detail

別のファイルにおいて、同じ名前で別アセットをロードした場合は、**先にロードされたアセットが優先される**。そのため、後にロードされたアセットは無視されるので、注意が必要。

具体例をあげる。

1. シーンAでMP3ファイル「bgm.mp3」を`bgm`という名前でロードする。
2. シーンBでMP3ファイル「hitSound.mp3」を`bgm`という名前でロードする。
3. シーンAはシーンBよりも先に実行される。
4. シーンBで`bgm`を再生すると、「bgm.mp3」が再生される（本来は「hitSound.mp3」が再生されてほしかった）

とりあえず、これ自体は仕様なので覚えておけばよい。大抵は名称設定を間違っている（先行する別ファイルと同じ名前をつけてしまった）ケースが多い。そのシーンでしか利用しないアセットなら、名前にシーン名をつけてやれば、重複しないはず。上記の例で言えば「sceneABGM」と「sceneBBGM」のように名称設定すればいい。

### reference

1. [なし]()
