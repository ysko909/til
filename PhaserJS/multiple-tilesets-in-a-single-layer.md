## 複数のタイルセットから構成される1枚のマップを読み込む方法

### detail

フィールドを作成するとき、複数のタイルセットを使って1枚のタイルマップを作ることがある。この場合、Phaser3でどのようにロードすればいいか。

```typescript
public preload(): void {
    this.load.tilemapTiledJSON('map', 'hoge.json');
    this.load.image('mapTiles', 'hoge.png');
}

public create(): void {

    this.cameras.main.fadeIn(1000, 0, 0, 0);

    let map = this.add.tilemap('map');
    let tileset = map.addTilesetImage('white_and_black', 'mapTiles');
    let mapGround = map.createLayer('mapGround', tileset);
    let mapObject = map.createLayer('mapObject', tileset);
    let mapEvent = map.createLayer('mapEvent', tileset);

    mapObject.setCollisionByProperty({ collides: true });
    mapEvent.setCollisionByProperty({ collides: true });

}

```
1枚のタイルセットから1枚のタイルマップを作成した場合は、上記のようにロードする。

```typescript
public preload(): void {

    this.load.tilemapTiledJSON('map', 'hoge.json');
    this.load.image('hoge1', 'hoge1.png');
    this.load.image('hoge2', 'hoge2.png');
    this.load.image('hoge3', 'hoge3.png');
    this.load.image('hoge4', 'hgoe4.png');

}

public create(): void {

    let map = this.add.tilemap('map');

    let allTileLayers: Phaser.Tilemaps.Tileset[] = [];

    ['hgoe1', 'hoge2', 'hoge3', 'hoge4'].forEach(tile => {
        allTileLayers.push(map.addTilesetImage(tile, tile));
    });
    
    let mapGround = map.createLayer('mapRoomFloor', allTileLayers);
    let mapWalls = map.createLayer('mapRoomWalls', allTileLayers);
    let mapObject = map.createLayer('mapRoomObject', allTileLayers);
    let mapObject2 = map.createLayer('mapRoomObject2', allTileLayers);
    let mapEvent = map.createLayer('mapRoomEvent', allTileLayers);

}
```

複数のタイルセットから構成される1枚のタイルマップのロードは、上記のように**タイルセットの配列**を使う。`addTilesetImage()`したタイルセットを配列にセットすることで、1つのタイルセットとして扱えるようになる。


### reference

1. [Multiple tilesets in a single Tiled layer?](https://www.html5gamedevs.com/topic/36520-multiple-tilesets-in-a-single-tiled-layer/)

