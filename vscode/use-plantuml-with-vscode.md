# vscodeでPlantUMLを使う

## detail

クラス図やアクティビティ図などは、[Draw.io](https://www.draw.io/index.html)のようなドローソフトやExcelなんかでも書けたりする。ただ、画像ファイル形式だと変更が必要になったときに面倒だったり、変更箇所の確認がしにくいなど、管理上問題がある。

そこで、テキストベースでチャートが書ける[PlantUML](https://plantuml.com/)を検討したい。vscodeでMarkdown書いてる中に、PlantUMLの文法でチャートを埋め込めるので基本的にはテキストベースに扱える。プレビューすればちゃんとチャートとして表示されるうえ、コードを変更すればホットリロードでプレビュー側も更新してくれる。よって、普通のMarkdownとして変更箇所の確認もできるため管理上かなり楽になる。

````markdown
```plantuml
@startuml

start
:hoge;
:fuga;
:piyo;
end

@enduml
```
````

PlantUMLをMarkdown中に記述する場合は、上記のほうに`@startuml`と`@enduml`で囲った中に表示したい図をPlantUMLの文法に従って記述すればいい。上記だとアクティビティ図（フローチャートっぽいやつ）になる。他の図については、[オフィシャル](https://plantuml.com/ja/)に様々なサンプルがあるので、それを参考にすればいい。

## reference

1. [PlantUML](https://plantuml.com/ja/)
2. [アクティビティ図](https://plantuml.com/ja/activity-diagram-beta)
