# ベクトルタイル仕様書

このドキュメントに記載されている単語 "しなければならない (MUST)"、"してはならない (MUST NOT)"、"要求されている (REQUIRED)"、"することになる (SHALL)"、"することはない (SHALL NOT)"、"する必要がある (SHOULD)"、"しないほうがよい (SHOULD NOT)"、"推奨される (RECOMMENDED)"、"してもよい (MAY)"、"選択できる (OPTIONAL)" は、 [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) に記述されているとおりに解釈されるものとする。

<!--
## 1. Purpose
-->

## 1. 目的

<!--
This document specifies a space-efficient encoding format for tiled geographic vector data. It is designed to be used in browsers or server-side applications for fast rendering or lookups of feature data.
-->

このドキュメントでは、タイル化された地理的ベクトルデータの容量効率のよい符号化フォーマットの仕様を策定する。この仕様は高速なレンダリングや地物データ検索をブラウザ及びサーバーサイドアプリケーションで実現するために設計されている。

<!--
## 2. File Format
-->

## 2. ファイルフォーマット

<!--
The Vector Tile format uses [Google Protocol Buffers](https://developers.google.com/protocol-buffers/) as a encoding format. Protocol Buffers are a language-neutral, platform-neutral extensible mechanism for serializing structured data.
-->

このベクトルタイルフォーマットは、[Google Protocol Buffers](https://developers.google.com/protocol-buffers/) を符号化フォーマットとして使用する。
Protocol Buffers は、構造化されたデータを格納するための特定の言語やプラットフォームに依存しない拡張可能な仕組みである。

<!--
### 2.1. File Extension
-->

### 2.1. ファイルの拡張子

<!--
The filename extension for Vector Tile files SHOULD be `mvt`. For example, a file might be named `vector.mvt`.
-->

ベクトルタイルファイルの拡張子は `mvt` にする必要がある (SHOULD)。たとえば、あるファイルは `vector.mvt` となるであろう。

<!--
### 2.2. Multipurpose Internet Mail Extensions (MIME)
-->

### 2.2. MIME タイプ (MIME)

<!--
When serving Vector Tiles the MIME type SHOULD be `application/vnd.mapbox-vector-tile`.
-->

ベクトルタイルを配信する際の MIME タイプは `application/vnd.mapbox-vector-tile` とするべきである (SHOULD)。

<!--
## 3. Projection and Bounds
-->

## 3. 投影法及び領域

<!--
A Vector Tile represents data based on a square extent within a projection. A Vector Tile SHOULD NOT contain information about its bounds and projection. The file format assumes that the decoder knows the bounds and projection of a Vector Tile before decoding it.
-->

ベクトルタイルは特定の投影法のもとでの四角い領域のデータを表現する。ベクトルタイルには、その領域や投影法に関する情報を含めないほうがよい (SHOULD NOT)。このファイル形式は、デコーダーがこれを復号化する前に領域や投影法を知っていることを前提としている。

<!--
[Web Mercator](https://en.wikipedia.org/wiki/Web_Mercator) is the projection of reference, and [the Google tile scheme](http://www.maptiler.org/google-maps-coordinates-tile-bounds-projection/) is the tile extent convention of reference. Together, they provide a 1-to-1 relationship between a specific geographical area, at a specific level of detail, and a path such as `https://example.com/17/65535/43602.mvt`.
-->

[Web メルカトル](https://en.wikipedia.org/wiki/Web_Mercator) は基準となる投影法であり、[the Google tile scheme](http://www.maptiler.org/google-maps-coordinates-tile-bounds-projection/) は基準となるタイル領域の慣例である。これらは、特定の詳細レベルにおける特定の地理的領域と `https://example.com/17/65535/43602.mvt` のようなパスを 1:1 で紐付ける。

<!--
Vector Tiles MAY be used to represent data with any projection and tile extent scheme.
-->

ベクトルタイルは、どのような投影法やタイル領域のスキームを用いた地理データの表現にも使用してもよい (MAY)。

<!--
## 4. Internal Structure
-->

## 4. 内部構造

<!--
This specification describes the structure of data within a Vector Tile. The reader should have an understanding of the [Vector Tile protobuf schema document](vector_tile.proto) and the structures it defines.
-->

この仕様書では、ベクトルタイル内のデータ構造について明らかにする。読者は [Vector Tile protobuf schema document](vector_tile.proto) と、それが定義する構造について理解している必要がある。

<!--
### 4.1. Layers
-->

### 4.1. レイヤー

<!--
A Vector Tile consists of a set of named layers. A layer contains geometric features and their metadata. The layer format is designed so that the data required for a layer is contiguous in memory, and so that layers can be appended to a Vector Tile without modifying existing data.
-->

ベクトルタイルは、名前付きのレイヤーのセットで構成されている。レイヤーは幾何属性のある地物とそのメタデータを含んでいる。レイヤのフォーマットは、ひとつのレイヤに必要なデータが記憶領域内で接するように設計されている。このようにすることによって、既存のデータを変更することなく、レイヤがベクトルタイルに追加できる。

<!--
A Vector Tile SHOULD contain at least one layer. A layer SHOULD contain at least one feature.
-->

ベクトルタイルは少なくともひとつのレイヤーを含む必要がある。レイヤーは少なくとも1つの地物を含むべきである。

<!--
A layer MUST contain a `version` field with the major version number of the Vector Tile specification to which the layer adheres. For example, a layer adhering to version 2.1 of the specification contains a `version` field with the integer value `2`. The `version` field SHOULD be the first field within the layer. Decoders SHOULD parse the `version` first to ensure that they are capable of decoding each layer. When a Vector Tile consumer encounters a Vector Tile layer with an unknown version, it MAY make a best-effort attempt to interpret the layer, or it MAY skip the layer. In either case it SHOULD continue to process subsequent layers in the Vector Tile.
-->

レイヤーは、そのレイヤーが準拠するベクトルタイル仕様書と同じメジャーバージョン番号を持つ `version` フィールドを含んでいなければならない (MUST)。たとえば、あるレイヤーがバージョン 2.1 に準拠しているのであれば、整数の `2` を `version` フィールドとして含めるべきである。`version` フィールドは、そのレイヤーの最初のフィールドである必要がある (SHOULD)。デコーダーは、各レイヤーを確実に処理するために、まず `version` をパースする必要がある (SHOULD)。ベクトルタイルのコンシューマーが未知のバージョンのベクトルタイルに遭遇した場合、レイヤーを解釈するために最善の努力をするか、そのレイヤーをスキップしてもよい (MAY)。どちらの場合も、そのベクトルタイル内の後続のレイヤーの処理を続ける必要がある (SHOULD)。

<!--
A layer MUST contain a `name` field. A Vector Tile MUST NOT contain two or more layers whose `name` values are byte-for-byte identical. Prior to appending a layer to an existing Vector Tile, an encoder MUST check the existing `name` fields in order to prevent duplication.
-->

レイヤーは `name` フィールドを含んでいなければならない (MUST)。ベクトルタイルは、同じ値 (byte-for-byte) を名前に持つ2つ以上のレイヤーを含んではならない (MUST NOT)。エンコーダーは、既存のベクトルタイルにレイヤーを追加する前に、重複を防止するために既存の `name` フィールドをチェックしなければならない (MUST)。

<!--
Each feature in a layer (see below) may have one or more key-value pairs as its metadata. The keys and values are indices into two lists, `keys` and `values`, that are shared across the layer's features.
-->

レイヤー内の地物（以下を参照）は一つまたは複数のキーと値のペアをメタデータとして保持してもよい (MAY)。キーと値は `keys` と `values` という2つのリストのインデックスで、レイヤーの地物をまたがって共有される。

<!--
Each element in the `keys` field of the layer is a string. The `keys` include all the keys of features used in the layer, and each key may be referenced by its positional index in this set of `keys`, with the first key having an index of 0. The set of `keys` SHOULD NOT contain two or more values which are byte-for-byte identical.
-->

`keys` フィールド内の個々の要素は文字列である。`keys` は、レイヤー内で使用される地物のすべてのキーを含んでおり、それぞれのキーは位置インデックスによって参照され、最初のキーは 0 というインデックスをもっている。`keys` のセットは、2つ以上の同じ値 (byte-for-byte) を含まないほうがよい (SHOULD NOT)。

<!--
Each element in the `values` field of the layer encodes a value of any of several types (see below). The `values` represent all the values of features used in the layer, and each value may be referenced by its positional index in this set of `values`, with the first value having an index of 0. The set of `values` SHOULD NOT contain two or more values of the same type which are byte-for-byte identical.
-->

`values` フィールド内のそれぞれの要素は、あらゆるデータ型がエンコードされている（以下を参照）。`values` は、レイヤー内で使用されるすべての値が再現されており、それぞれのキーは位置インデックスによって参照され、最初のキーは 0 というインデックスをもっている。`values` のセットは、2つ以上の同じ値 (byte-for-byte) を含まないほうがよい (SHOULD NOT)。

<!--
In order to support values of varying string, boolean, integer, and floating point types, the protobuf encoding of the `value` field consists of a set of `optional` fields. A value MUST contain exactly one of these optional fields.
-->

boolean 型、integer 型、そして浮動小数点型などの異なるデータ型をサポートするために、`value` フィールドの Protocol Buffers エンコーディングは、`optional` フィールドのセットで構成されている。値はこれらのオプションフィールドのうちの一つだけが含まれなければならない (MUST)。

<!--
A layer MUST contain an `extent` that describes the width and height of the tile in integer coordinates. The geometries within the Vector Tile MAY extend past the bounds of the tile's area as defined by the `extent`. Geometries that extend past the tile's area as defined by `extent` are often used as a buffer for rendering features that overlap multiple adjacent tiles.
-->

レイヤーはタイルの幅と高さを記述するための整数による座標 `extent` を含んでいなければならない (MUST)。ベクタータイル内のジオメトリは、`extent` で定義されたタイル領域の境界を越えて延びてもよい (MAY)。`extent` で定義されたタイル領域を越えて延びるジオメトリは、隣接する複数のタイルに重なる地物をレンダリングするためのバッファとして使用されることが多い。

<!--
For example, if a tile has an `extent` of 4096, coordinate units within the tile refer to 1/4096th of its square dimensions. A coordinate of 0 is on the top or left edge of the tile, and a coordinate of 4096 is on the bottom or right edge. Coordinates from 1 through 4095 inclusive are fully within the extent of the tile, and coordinates less than 0 or greater than 4096 are fully outside the extent of the tile.  A point at `(1,10)` or `(4095,10)` is within the extent of the tile. A point at `(0,10)` or `(4096,10)` is on the edge of the extent. A point at `(-1,10)` or `(4097,10)` is outside the extent of the tile.
-->

たとえば、あるタイルが 4096 という値の `extent` を持っている場合、タイル内の座標は正方形の 1/4096 となる。座標 0 はタイルの左上の角にあり、4096 は右下の角にある。1 から 4095 までの座標は完全にタイルの領域内にあり、0 より小さいか 4096 より大きい座標はタイルの領域外である。座標 `(1,10)` または `(4095,10)` のポイントはタイルの領域内にあり、`(0,10)` または `(4096,10)` のポイントはタイルの領域の端にある。`(-1,10)` または `(4097,10)` のポイントはタイルの領域外にある。

<!--
### 4.2. Features
-->

### 4.2. 地物

<!--
A feature MUST contain a `geometry` field.
-->

地物は、`geometry` フィールドを含まなければならない (MUST)。

<!--
A feature MUST contain a `type` field as described in the Geometry Types section.
-->

地物は、Geometry 型セクションで説明している `type` フィールドを含まなければならない (MUST)。

<!--
A feature MAY contain a `tags` field. Feature-level metadata, if any, SHOULD be stored in the `tags` field.
-->

地物は、`tags` フィールドを含むことができる (MAY)。地物固有のメタデータがもしあれば、`tags` フィールドに保存されるべきである (SHOULD)。

<!--
A feature MAY contain an `id` field. If a feature has an `id` field, the value of the `id` SHOULD be unique among the features of the parent layer.
-->

地物は、`id` フィールドを含むことができる (MAY)。もし地物が `id` フィールドを持っている場合、`id` フィールドの値は、親レイヤーの中で地物ごとにユニークであるべきである (SHOULD)。

<!--
### 4.3. Geometry Encoding
-->

### 4.3. ジオメトリエンコーディング

<!--
Geometry data in a Vector Tile is defined in a screen coordinate system. The upper left corner of the tile (as displayed by default) is the origin of the coordinate system. The X axis is positive to the right, and the Y axis is positive downward. Coordinates within a geometry MUST be integers.
-->

ベクトルタイル内のジオメトリデータは、スクリーンの座標システムで定義される。（デフォルトで表示される）タイルの最も左上は、座標システムの原点である。X 座標は右に正であり、Y 座標は下に正である。ジオメトリ内の座標は整数でなければならない (MUST)。

<!--
A geometry is encoded as a sequence of 32 bit unsigned integers in the `geometry` field of a feature. Each integer is either a `CommandInteger` or a `ParameterInteger`. A decoder interprets these as an ordered series of operations to generate the geometry.
-->

ジオメトリは、地物の `geometry` フィールド内に、32 bit 符号なし整数のシーケンスとしてエンコードされる。各整数は、`CommandInteger` か `ParameterInteger` である。デコーダはこれらをジオメトリを生成するために順番に処理するものとして解釈する。

<!--
Commands refer to positions relative to a "cursor", which is a redefinable point. For the first command in a feature, the cursor is at `(0,0)` in the coordinate system. Some commands move the cursor, affecting subsequent commands.
-->

コマンドは、"カーソル"位置を再定義可能な点である相対的な位置で参照する。地物の最初のコマンドでは、カーソルは座標システムでの `(0,0)` にある。一部のコマンドはカーソルを移動し、その後のコマンドに影響を与える。

#### 4.3.1. Command Integers

<!--
A `CommandInteger` indicates a command to be executed, as a command ID, and the number of times that the command will be executed, as a command count.
-->

`CommandInteger` は、コマンド ID で実行されるコマンドを指示し、コマンドカウントで実行されるコマンドの実行回数を指示する。

<!--
A command ID is encoded as an unsigned integer in the least significant 3 bits of the `CommandInteger`, and is in the range 0 through 7, inclusive. A command count is encoded as an unsigned integer in the remaining 29 bits of a `CommandInteger`, and is in the range `0` through `pow(2, 29) - 1`, inclusive.
-->

コマンド ID は、`CommandInteger`の最下位 3 ビットの符号なし整数として符号化され、0〜7 の範囲内にある。 コマンドカウントは、`CommandInteger` の残りの29ビットの符号なし整数として符号化され、`0` から `pow（2, 29）- 1` の範囲内にある。

<!--
A command ID, a command count, and a `CommandInteger` are related by these bitwise operations:
-->

コマンド ID、コマンドカウント、および `CommandInteger` は、これらのビット操作によって関連付けられる:

```javascript
CommandInteger = (id & 0x7) | (count << 3)
```

```javascript
id = CommandInteger & 0x7
```

```javascript
count = CommandInteger >> 3
```

<!--
A command ID specifies one of the following commands:
-->

コマンド ID は、以下のコマンドのうちの一つを指定する:

|  Command     |  Id  | Parameters    | Parameter Count |
| ------------ |:----:| ------------- | --------------- |
| MoveTo       | `1`  | `dX`, `dY`    | 2               |
| LineTo       | `2`  | `dX`, `dY`    | 2               |
| ClosePath    | `7`  | No parameters | 0               |

<!--
##### Example Command Integers
-->

##### Command Integers の例

| Command   |  ID  | Count | CommandInteger | Binary Representation `[Count][Id]`      |
| --------- |:----:|:-----:|:--------------:|:----------------------------------------:|
| MoveTo    | `1`  | `1`   | `9`            | `[00000000 00000000 0000000 00001][001]` |
| MoveTo    | `1`  | `120` | `961`          | `[00000000 00000000 0000011 11000][001]` |
| LineTo    | `2`  | `1`   | `10`           | `[00000000 00000000 0000000 00001][010]` |
| LineTo    | `2`  | `3`   | `26`           | `[00000000 00000000 0000000 00011][010]` |
| ClosePath | `7`  | `1`   | `15`           | `[00000000 00000000 0000000 00001][111]` |

<!--
#### 4.3.2. Parameter Integer
-->

#### 4.3.2. 数値パラメーター

<!--
Commands requiring parameters are followed by a `ParameterInteger` for each parameter required by that command. The number of `ParameterIntegers` that follow a `CommandInteger` is equal to the parameter count of a command multiplied by the command count of the `CommandInteger`. For example, a `CommandInteger` with a `MoveTo` command with a command count of 3 will be followed by 6 `ParameterIntegers`.
-->

パラメータを必要とするコマンドの後には、そのコマンドで必要とされる各パラメータのための `ParameterInteger` がつづく。`CommandInteger` につづく `ParameterIntegers`の数は、 `CommandInteger` のコマンドカウントを掛けたコマンドのパラメータ数に等しい。たとえば、コマンドカウントが3の `MoveTo` コマンドを含む `CommandInteger` の後に6つの `ParameterIntegers` が続く。

<!--
A `ParameterInteger` is [zigzag](https://developers.google.com/protocol-buffers/docs/encoding#types) encoded so that small negative and positive values are both encoded as small integers. To encode a parameter value to a `ParameterInteger` the following formula is used:
-->

数値パラメータは [ZigZag](https://developers.google.com/protocol-buffers/docs/encoding#types) でエンコードされており、小さな正と負の両方の整数がそれぞれ小さな整数としてエンコードされる。数値パラメータの値を `ParameterInteger` にエンコードするには以下の数式を使用する。

```javascript
ParameterInteger = (value << 1) ^ (value >> 31)
```

<!--
Parameter values greater than `pow(2,31) - 1` or less than `-1 * (pow(2,31) - 1)` are not supported.
-->

`pow(2,31) - 1` よりも大きい、または `-1 * (pow(2,31) - 1)` よりも小さいパラメータはサポートされていない。

<!--
The following formula is used to decode a `ParameterInteger` to a value:
-->

以下の関数は、`ParameterInteger` を値にデコードするものである。

```javascript
value = ((ParameterInteger >> 1) ^ (-(ParameterInteger & 1)))
```

<!--
#### 4.3.3. Command Types
-->

#### 4.3.3. コマンドタイプ

<!--
For all descriptions of commands the initial position of the cursor shall be described to be at the coordinates `(cX, cY)` where `cX` is the position of the cursor on the X axis and `cY` is the position of the `cursor` on the Y axis.
-->

コマンドの描写を行うための、カーソルの初期位置は `(cX、cY)` 座標で記述され、 `cX` は X 軸上のカーソルの位置であり、`cY` は `cursor` を Y 軸上に移動させる。

<!--
##### 4.3.3.1. MoveTo Command
-->

##### 4.3.3.1. MoveTo コマンド

<!--
A `MoveTo` command with a command count of `n` MUST be immediately followed by `n` pairs of `ParameterInteger`s. Each pair `(dX, dY)`:
-->

コマンド回数 `n` を持つ `MoveTo` コマンドは、`ParameterInteger` の `n` の値の直後に続かなければならない (MUST)。

<!--
1. Defines the coordinate `(pX, pY)`, where `pX = cX + dX` and `pY = cY + dY`.
   * Within POINT geometries, this coordinate defines a new point.
   * Within LINESTRING geometries, this coordinate defines the starting vertex of a new line.
   * Within POLYGON geometries, this coordinate defines the starting vertex of a new linear ring.
2. Moves the cursor to `(pX, pY)`.
-->

1. `pX = cX + dX` と `pY = cY + dY` で、`(pX, pY)` を座標を定義する。
   * POINT ジオメトリ内のこの座標は新しいポイントを定義する。
   * LINESTRING ジオメトリ内のこの座標は新しい行の開始頂点を定義する。
   * POLYGON ジオメトリ内のこの座標は新しい線形リングの開始頂点を定義する。
2. カーソルを `(pX, pY)` に移動する。

<!--
##### 4.3.3.2. LineTo Command
-->

##### 4.3.3.2. LineTo コマンド

<!--
A `LineTo` command with a command count of `n` MUST be immediately followed by `n` pairs of `ParameterInteger`s. Each pair `(dX, dY)`:
-->

コマンド回数 `n` を持つ `LineTo` コマンドは、`ParameterInteger` の `n` の値の直後に続かなければならない (MUST)。

<!--
1. Defines a segment beginning at the cursor `(cX, cY)` and ending at the coordinate `(pX, pY)`, where `pX = cX + dX` and `pY = cY + dY`.
   * Within LINESTRING geometries, this segment extends the current line.
   * Within POLYGON geometries, this segment extends the current linear ring.
2. Moves the cursor to `(pX, pY)`.
-->

1. `pX = cX + dX` と `pY = cY + dY` で、`(cX, cY)` で始まり `(pX, pY)` で終わるセグメントを定義する。
   * LINESTRING ジオメトリ内のこのセグメントは現在の行を拡張する。
   * POLYGON ジオメトリ内のこのセグメントは現在の linear ring を拡張する。
2. カーソルを `(pX, pY)` に移動する。

<!--
For any pair of `(dX, dY)` the `dX` and `dY` MUST NOT both be `0`.
-->

`(dX, dY)` の `dX` と `dY` のペアは、どちらも 0 であってはならない (MUST NOT)。

<!--
##### 4.3.3.3. ClosePath Command
-->

##### 4.3.3.3. ClosePath コマンド

<!--
A `ClosePath` command MUST have a command count of 1 and no parameters. The command closes the current linear ring of a POLYGON geometry via a line segment beginning at the cursor `(cX, cY)` and ending at the starting vertex of the current linear ring.
-->

`ClosePath` コマンドは、パラメータなし及びコマンドカウント 1 を値として持たなくてはならない (MUST)。このコマンドは、カーソル `(cX、cY)`で始まり、現在の linear ring の開始頂点で終わる線分を介して POLYGON ジオメトリの現在の線形リングを閉じる。

<!--
This command does not change the cursor position.
-->

このコマンドは、カーソル位置を変更しない。

<!--
#### 4.3.4. Geometry Types
-->

#### 4.3.4. ジオメトリタイプ

<!--
The `geometry` field is described in each feature by the `type` field which must be a value in the enum `GeomType`. The following geometry types are supported:
-->

`geometry` フィールドは、`GeomType` 内に存在する値からなる `type` にて、各フィーチャーに記述されている。

* UNKNOWN
* POINT
* LINESTRING
* POLYGON

<!--
Geometry collections are not supported.
-->

ジオメトリコレクションはサポートされていない。

<!--
##### 4.3.4.1. Unknown Geometry Type
-->

##### 4.3.4.1. 未知のジオメトリタイプ

<!--
The specification purposefully leaves an unknown geometry type as an option. This geometry type encodes experimental geometry types that an encoder MAY choose to implement. Decoders MAY ignore any features of this geometry type.
-->

この仕様では、オプションとして意図的に未知のジオメトリタイプが残されている。このジオメトリタイプは、実験的なジオメトリタイプをエンコーダがエンコードすることもできる。デコーダはこのジオメトリタイプの特徴を無視してもよい (MAY)。

<!--
##### 4.3.4.2. Point Geometry Type
-->

##### 4.3.4.2. Point ジオメトリタイプ

<!--
The `POINT` geometry type encodes a point or multipoint geometry. The geometry command sequence for a point geometry MUST consist of a single `MoveTo` command with a command count greater than 0.

If the `MoveTo` command for a `POINT` geometry has a command count of 1, then the geometry MUST be interpreted as a single point; otherwise the geometry MUST be interpreted as a multipoint geometry, wherein each pair of `ParameterInteger`s encodes a single point.
-->

`POINT`ジオメトリタイプはポイントまたはマルチポイントジオメトリをエンコードする。 ポイントジオメトリのジオメトリコマンドシーケンスは、コマンドカウントが 0 より大きい単一の `MoveTo` コマンドで構成されなければならない (MUST)。

`POINT` ジオメトリの `MoveTo` コマンドが 1 のコマンドカウントを持つ場合、ジオメトリは単一ポイントとして解釈されなくてはならない (MUST)。 さもなければ、ジオメトリはマルチポイントジオメトリとして解釈されなければならない (MUST)。ここでは、 `ParameterInteger` の各ペアは単一のポイントをエンコードする。

<!--
##### 4.3.4.3. Linestring Geometry Type
-->

##### 4.3.4.3. Linestring ジオメトリ

<!--
The `LINESTRING` geometry type encodes a linestring or multilinestring geometry. The geometry command sequence for a linestring geometry MUST consist of one or more repetitions of the following sequence:
-->

`LINESTRING` ジオメトリタイプは、linestring または multilinestring ジオメトリをエンコードする。linestring ジオメトリのジオメトリコマンドシーケンスは、次のシーケンスの 1 つ以上の反復で構成されなければならない  (MUST):

<!--
1. A `MoveTo` command with a command count of 1
2. A `LineTo` command with a command count greater than 0
-->

1. `MoveTo` コマンド及び １ のコマンドカウント
2. `LineTo` コマンド及び 0 よりも大きいコマンドカウント

<!--
If the command sequence for a `LINESTRING` geometry type includes only a single `MoveTo` command then the geometry MUST be interpreted as a single linestring; otherwise the geometry MUST be interpreted as a multilinestring geometry, wherein each `MoveTo` signals the beginning of a new linestring.
-->

`LINESTRING` ジオメトリタイプのコマンドシーケンスが単一の `MoveTo` コマンドのみを含む場合、ジオメトリは単一の linestring として解釈されなければならない (MUST)。それ以外の場合は、ジオメトリを multilinestring ジオメトリとして解釈しなければならない (MUST)。それぞれの `MoveTo` は、新しい linestring 開始のきっかけとなる。

<!--
##### 4.3.4.4. Polygon Geometry Type
-->

##### 4.3.4.4. Polygon ジオメトリタイプ

<!--
The `POLYGON` geometry type encodes a polygon or multipolygon geometry, each polygon consisting of exactly one exterior ring that contains zero or more interior rings. The geometry command sequence for a polygon consists of one or more repetitions of the following sequence:
-->

`POLYGON` ジオメトリタイプは、polygon または multipolygon ジオメトリをエンコードする。それぞれの polygon は、確実に 0 またはそれ以上の内部境界を含む 1 つの外部境界から構成される。Polygon のジオメトリコマンドシーケンスは、次のシーケンスの 1 回以上の繰り返しから構成される。

<!--
1. An `ExteriorRing`
2. Zero or more `InteriorRing`s
-->

1. 単一の `ExteriorRing`
2. 0 またはそれ以上の `InteriorRing`

<!--
Each `ExteriorRing` and `InteriorRing` MUST consist of the following sequence:
-->

それぞれの `ExteriorRing` と `InteriorRing` は、以下のシーケンスから構成されなければならない。

<!--
1. A `MoveTo` command with a command count of 1
2. A `LineTo` command with a command count greater than 1
3. A `ClosePath` command
-->

1. 単一の `MoveTo` コマンド及び 1 のコマンドカウント
2. 単一の `LineTo` コマンド及び 1 以上のコマンドカウント
3. 単一の `ClosePath` コマンド

<!--
An exterior ring is DEFINED as a linear ring having a positive area as calculated by applying the [surveyor's formula](https://en.wikipedia.org/wiki/Shoelace_formula) to the vertices of the polygon in tile coordinates. In the tile coordinate system (with the Y axis positive down and X axis positive to the right) this makes the exterior ring's winding order appear clockwise.
-->

外部境界は、linear ring として定義される。この linear ring は、[surveyor's formula](https://en.wikipedia.org/wiki/Shoelace_formula) をタイル座標のポリゴンの頂点に適用することによって計算される正のエリアをもつ。タイル座標系（Y 軸が下向きの正方向、X 軸が右向きの正方向）では、これにより外部境界の線の順序が時計回りになる。

<!--
An interior ring is DEFINED as a linear ring having a negative area as calculated by applying the [surveyor's formula](https://en.wikipedia.org/wiki/Shoelace_formula) to the vertices of the polygon in tile coordinates. In the tile coordinate system (with the Y axis positive down and X axis positive to the right) this makes the interior ring's winding order appear counterclockwise.
-->

内部境界は、この linear ring は、[surveyor's formula](https://en.wikipedia.org/wiki/Shoelace_formula) をタイル座標のポリゴンの頂点に適用することによって計算される負のエリアをもつ。タイル座標系（Y 軸が下向きの正方向、X 軸が右向きの正方向）では、これにより外部境界の線の順序が反時計回りになる。

<!--
If the command sequence for a `POLYGON` geometry type includes only a single exterior ring then the geometry MUST be interpreted as a single polygon; otherwise the geometry MUST be interpreted as a multipolygon geometry, wherein each exterior ring signals the beginning of a new polygon. If a polygon has interior rings they MUST be encoded directly after the exterior ring of the polygon to which they belong.
-->

`POLYGON` ジオメトリタイプのコマンドシーケンスが単一の外部境界のみを含んでいる場合、そのジオメトリは単一のポリゴンとして解釈されなければならない (MUST)。そうでなければ、そのジオメトリは multipolygon ジオメトリとして解釈されなければならない (MUST)。その場合、それぞれの外部境界が出現することがあ新しいポリゴンの開始を意味する。ポリゴンが内部境界を保つ場合、その内部境界はそれが属するポリゴンの外部境界の直後で符号化されなければならない（MUST）。

<!--
Linear rings MUST be geometric objects that have no anomalous geometric points, such as self-intersection or self-tangency. The position of the cursor before calling the `ClosePath` command of a linear ring SHALL NOT repeat the same position as the first point in the linear ring as this would create a zero-length line segment. A linear ring SHOULD NOT have an area calculated by the surveyor's formula equal to zero, as this would signify a ring with anomalous geometric points.
-->

Linear rings は、self-intersection や self-tangency などの変則なジオメトリックポイントを持たないジオメトリックオブジェクトであるべきである。linear ring の `ClosePath` コマンドを呼び出す前のカーソル位置は、長さが 0 にならないようにするために linear ring の最初の点と同じ位置になることはない (SHALL NOT)。

<!--
Polygon geometries MUST NOT have any interior rings that intersect and interior rings MUST be enclosed by the exterior ring.
-->

Polygon ジオメトリは、交差する内部境界を持ってはならない (MUST NOT)。内部境界を外部境界で囲まなければならない (MUST)。

<!--
#### 4.3.5. Example Geometry Encodings
-->

#### 4.3.5. ジオメトリエンコーディングの例

<!--
##### 4.3.5.1. Example Point
-->

##### 4.3.5.1. Point の例

<!--
An example encoding of a point located at:
-->

Point のエンコーディングの例:

* (25,17)

<!--
This would require a single command:
-->

これは以下の単一のコマンドを必要とする:

* MoveTo(+25, +17)

```
Encoded as: [ 9 50 34 ]
              | |  `> Decoded: ((34 >> 1) ^ (-(34 & 1))) = +17
              | `> Decoded: ((50 >> 1) ^ (-(50 & 1))) = +25
              | ===== relative MoveTo(+25, +17) == create point (25,17)
              `> [00001 001] = command id 1 (MoveTo), command count 1
```

<!--
##### 4.3.5.2. Example Multi Point
-->

##### 4.3.5.2. マルチポイントの例

<!--
An example encoding of two points located at:
-->

2つのポイントのエンコーディングの例:

* (5,7)
* (3,2)

<!--
This would require two commands:
-->

これは2つのコマンドを必要とする:

* MoveTo(+5,+7)
* MoveTo(-2,-5)

```
Encoded as: [ 17 10 14 3 9 ]
               |  |  | | `> Decoded: ((9 >> 1) ^ (-(9 & 1))) = -5
               |  |  | `> Decoded: ((3 >> 1) ^ (-(3 & 1))) = -2
               |  |  | === relative MoveTo(-2, -5) == create point (3,2)
               |  |  `> Decoded: ((34 >> 1) ^ (-(34 & 1))) = +7
               |  `> Decoded: ((50 >> 1) ^ (-(50 & 1))) = +5
               | ===== relative MoveTo(+25, +17) == create point (25,17)
               `> [00010 001] = command id 1 (MoveTo), command count 2
```

<!--
##### 4.3.5.3. Example Linestring
-->

##### 4.3.5.3. Linestring の例

<!--
An example encoding of a line with the points:
-->

点による線のエンコーディングの例:

* (2,2)
* (2,10)
* (10,10)

これは3つのコマンドを必要とする:

* MoveTo(+2,+2)
* LineTo(+0,+8)
* LineTo(+8,+0)

```
Encoded as: [ 9 4 4 18 0 16 16 0 ]
              |      |      ==== relative LineTo(+8, +0) == Line to Point (10, 10)
              |      | ==== relative LineTo(+0, +8) == Line to Point (2, 10)
              |      `> [00010 010] = command id 2 (LineTo), command count 2
              | === relative MoveTo(+2, +2)
              `> [00001 001] = command id 1 (MoveTo), command count 1
```

<!--
##### 4.3.5.4. Example Multi Linestring
-->

##### 4.3.5.4. Multi Linestring の例

<!--
An example encoding of two lines with the points:
-->

点による2本の線のエンコーディングの例:

* Line 1:
  * (2,2)
  * (2,10)
  * (10,10)
* Line 2:
  * (1,1)
  * (3,5)

<!--
This would require the following commands:
-->

これは以下のコマンドを必要とする:

* MoveTo(+2,+2)
* LineTo(+0,+8)
* LineTo(+8,+0)
* MoveTo(-9,-9)
* LineTo(+2,+4)

```
Encoded as: [ 9 4 4 18 0 16 16 0 9 17 17 10 4 8 ]
              |      |           |        | === relative LineTo(+2, +4) == Line to Point (3,5)
              |      |           |        `> [00001 010] = command id 2 (LineTo), command count 1
              |      |           | ===== relative MoveTo(-9, -9) == Start new line at (1,1)
              |      |           `> [00001 001] = command id 1 (MoveTo), command count 1
              |      |      ==== relative LineTo(+8, +0) == Line to Point (10, 10)
              |      | ==== relative LineTo(+0, +8) == Line to Point (2, 10)
              |      `> [00010 010] = command id 2 (LineTo), command count 2
              | === relative MoveTo(+2, +2)
              `> [00001 001] = command id 1 (MoveTo), command count 1
```

<!--
##### 4.3.5.5. Example Polygon
-->

##### 4.3.5.5. ポリゴンの例

<!--
An example encoding of a polygon feature that has the points:
-->

複数の点をもつポリゴンをエンコーディングする例:

* (3,6)
* (8,12)
* (20,34)
* (3,6) *Path Closing as Last Point*

<!--
Would encoded by using the following commands:
-->

これは以下のコマンドでエンコードされる:

* MoveTo(3, 6)
* LineTo(5, 6)
* LineTo(12, 22)
* ClosePath

```
Encoded as: [ 9 6 12 18 10 12 24 44 15 ]
              |       |              `> [00001 111] command id 7 (ClosePath), command count 1
              |       |       ===== relative LineTo(+12, +22) == Line to Point (20, 34)
              |       | ===== relative LineTo(+5, +6) == Line to Point (8, 12)
              |       `> [00010 010] = command id 2 (LineTo), command count 2
              | ==== relative MoveTo(+3, +6)
              `> [00001 001] = command id 1 (MoveTo), command count 1
```

<!--
##### 4.3.5.6. Example Multi Polygon
-->

##### 4.3.5.6. マルチポリゴンの例

<!--
An example of a more complex encoding of two polygons, one with a hole. The position of the points for the polygons are shown below. The winding order of the polygons is VERY important in this example as it signifies the difference between interior rings and a new polygon.
-->

2つのポリゴンを使用したより複雑なエンコーディングの例で一つは穴が開いている。ポリゴンの点の位置は以下のとおりである。この例では内部境界と新しいポリゴンの違いを表すためにポリゴンの巻き順が重要である。

* Polygon 1:
  * Exterior Ring:
    * (0,0)
    * (10,0)
    * (10,10)
    * (0,10)
    * (0,0) *Path Closing as Last Point*
* Polygon 2:
  * Exterior Ring:
    * (11,11)
    * (20,11)
    * (20,20)
    * (11,20)
    * (11,11) *Path Closing as Last Point*
  * Interior Ring:
    * (13,13)
    * (13,17)
    * (17,17)
    * (17,13)
    * (13,13) *Path Closing as Last Point*

<!--
This polygon would be encoded with the following set of commands:
-->

このポリゴンは以下のコマンドの組み合わせによってエンコードされる。

* MoveTo(+0,+0)
* LineTo(+10,+0)
* LineTo(+0,+10)
* LineTo(-10,+0) // Cursor at 0,10 after this command
* ClosePath // End of Polygon 1
* MoveTo(+11,+1) // NOTE THAT THIS IS RELATIVE TO LAST LINETO!
* LineTo(+9,+0)
* LineTo(+0,+9)
* LineTo(-9,+0) // Cursor at 11,20 after this command
* ClosePath // This is a new polygon because area is positive!
* MoveTo(+2,-7) // NOTE THAT THIS IS RELATIVE TO LAST LINETO!
* LineTo(+0,+4)
* LineTo(+4,+0)
* LineTo(+0,-4) // Cursor at 17,13
* ClosePath // This is an interior ring because area is negative!

<!--
### 4.4. Feature Attributes
-->

### 4.4. 地物の属性

<!--
Feature attributes are encoded as pairs of integers in the `tag` field of a feature. The first integer in each pair represents the zero-based index of the key in the `keys` set of the `layer` to which the feature belongs. The second integer in each pair represents the zero-based index of the value in the `values` set of the `layer` to which the feature belongs. Every key index MUST be unique within that feature such that no other attribute pair within that feature has the same key index. A feature MUST have an even number of `tag` fields. A feature `tag` field MUST NOT contain a key index or value index greater than or equal to the number of elements in the layer's `keys` or `values` set, respectively.
-->

地物の属性は、地物の `tag` フィールド内の数字のペアによってエンコードされる。各ペアの最初の整数は地物が属する `layer` の `keys` セットのキーの 0 から始まるインデックスを表す。各ペアの第二の整数は、地物が属する `layer` の `values` セット内の 0 から始まるインデックスを表す。すべてのキーインデックスは、その地物内の他の属性のペアが同じキーインデックスを持たないように、その地物内で一意でなければならない。地物は偶数の `tag` フィールドを持たなければならない (MUST)。地物の `tag` フィールドはレイヤーの `keys` または `values` セットの要素の数以上のキーインデックスまたは値インデックスを含んではならない (MUST NOT)。

<!--
### 4.5. Example
-->

### 4.5. 例

<!--
For example, a GeoJSON feature like:
-->

たとえば以下のような GeoJSON では:

```json
{
    "type": "FeatureCollection",
    "features": [
        {
            "geometry": {
                "type": "Point",
                "coordinates": [
                    -8247861.1000836585,
                    4970241.327215323
                ]
            },
            "type": "Feature",
            "properties": {
                "hello": "world",
                "h": "world",
                "count": 1.23
            }
        },
        {
            "geometry": {
                "type": "Point",
                "coordinates": [
                    -8247861.1000836585,
                    4970241.327215323
                ]
            },
            "type": "Feature",
            "properties": {
                "hello": "again",
                "count": 2
            }
        }
    ]
}
```

以下のような構造となる:

```js
layers {
  version: 2
  name: "points"
  features: {
    id: 1
    tags: 0
    tags: 0
    tags: 1
    tags: 0
    tags: 2
    tags: 1
    type: Point
    geometry: 9
    geometry: 2410
    geometry: 3080
  }
  features {
    id: 2
    tags: 0
    tags: 2
    tags: 2
    tags: 3
    type: Point
    geometry: 9
    geometry: 2410
    geometry: 3080
  }
  keys: "hello"
  keys: "h"
  keys: "count"
  values: {
    string_value: "world"
  }
  values: {
    double_value: 1.23
  }
  values: {
    string_value: "again"
  }
  values: {
    int_value: 2
  }
  extent: 4096
}
```

<!--
Keep in mind the exact values for the geometry would differ based on the projection and extent of the tile.
-->

ジオメトリの正確な値は、タイルの投影法および領域によって異なる。
