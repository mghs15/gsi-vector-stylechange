# 地理院地図Vectorのスタイルを<br>シンプルなMapbox GL JSで実装する試み
- 実装例　https://mghs15.github.io/gsi-vector-style-converter/map.html
- [改良版](https://github.com/mghs15/gsi-vector-style-converter#fill-pattern%E7%94%A8%E3%81%AE%E7%94%BB%E5%83%8F%E3%81%AE%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%81%BF)　（fill-patternの実現）
- Mapboxのサービスと合わせた例　https://mghs15.github.io/gsi-vector-style-converter/map2.html
（[Mapbox](https://www.mapbox.com/about/maps/)の提供する[OpenStreetMap](https://www.openstreetmap.org/about/)のPOIとlanduseを上乗せ）

※独自スタイルでは、建物を3D表示していますが、あくまでイメージです。その他、細かい調整等は行っておりません。

## モチベーション
[地理院地図Vector（仮称）提供実験](https://github.com/gsi-cyberjapan/gsimaps-vector-experiment)のスタイル設定ファイルは、
そのままではMapbox GL JSのスタイル設定ファイル（[Mapbox Style Specification](https://docs.mapbox.com/mapbox-gl-js/style-spec/)）に準拠していないため、なんとか簡単にMapbox Style Specification準拠へ変換する方法を考えてみた。

## 出来上がったツール

### ツールのURLとベースとしたサイト
**ツールのURL**　https://mghs15.github.io/gsi-vector-style-converter/tool

[地理院地図Vectorのサイト](https://maps.gsi.go.jp/vector/)を無理やり改造した。変更ファイルは 以下の通り。
- index.html
- [js/src/map/layer-binaryvectortile.js](https://mghs15.github.io/gsi-vector-style-converter/tool/js/src/map/layer-binaryvectortile.js) 
- css/common.css

※2020年3月21日 [地理院地図Vectorのアップデート](https://www.gsi.go.jp/johofukyu/johofukyu200318.html)に対応済みです。

### 使い方
1. 上記サイトで、変換したいスタイルをロードする。
	- おすすめの地図のほか、自分で作成したスタイルを読み込んでも良い。
2. ロードが完了すると、自動的にMapbox Gl JSが読み込める形のスタイルへ変換処理を行う。
	- 同時に、左のパネル中ほど、「ダウンロード」ボタンの左（場合によっては上）にロードしたスタイルのタイトルが（自分で作成したスタイルの場合は"（読込）"の文字も一緒に）表示される。
	- ラスタの情報や地理院地図Vector独自の実装については削除されるので注意。
	- 編集機能を用いてスタイルを変更しても、反映されないので注意。
3. 「ダウンロード」ボタンを押すと、Mapbox Gl JSが読み込める形のstyle.jsonがダウンロードされる。
4. ["sprite"](https://docs.mapbox.com/mapbox-gl-js/style-spec/#root-sprite)に設定されたspriteファイルのURLは、おすすめ地図の「標準地図、注記＋写真、大きい文字、標準地図②」以外は淡色地図用になるので、自分でスタイルを読み込む場合等は、必要に応じて、手動で標準地図用に変換する。
	- 標準地図用spriteファイルURL　https://cyberjapandata.gsi.go.jp/vector/sprite/std
	- 淡色地図用spriteファイルURL　https://cyberjapandata.gsi.go.jp/vector/sprite/pale
5. 「縦書き対応」にチェックを入れると、次にロードされるスタイルについて、地理院地図Vectorで縦書きの文字を縦書きとして表現できるように処理を行う。

### 手動での取出し

<details>
<summary>上記の使い方がうまくいかなったときの対応方法</summary>

各スタイルを読み込むとデベロッパーツールのコンソールにMapbox Style Specificationの["layers"](https://docs.mapbox.com/mapbox-gl-js/style-spec/#root-layers)に該当する部分の設定ファイルが出力されるので、
これをMapbox GL JSのスタイル設定ファイルにコピペしてあげればよい。

```
"layers":[

ここにコピペ

]
```

テンプレートはこちら→https://mghs15.github.io/gsi-vector-style-converter/template.json


コピペ後の各style.jsonには、以下の修正が必要
- ["sources"](https://docs.mapbox.com/mapbox-gl-js/style-spec/#root-sources)に必要なid（gsibv-vectortile-source-1-4-17など）を持ったデータソースの設定をしてあげる
- ["sprite"](https://docs.mapbox.com/mapbox-gl-js/style-spec/#root-sprite)の設定を適宜変更
- <gsi-vertical>タグを削除
	- 削除ツールはこちら（Perl製）→ https://github.com/mghs15/gsi-vector-style-converter/blob/master/perl/delete-gsi-vertical-tag.pl 
	- 本当は["concat"](https://docs.mapbox.com/mapbox-gl-js/style-spec/#expressions-concat)ごと削除したいが、JSONが崩れ、やる気をなくしたため保留。
- ["icon-image"](https://docs.mapbox.com/mapbox-gl-js/style-spec/#layout-symbol-icon-image)の値を整理
	- "std///田"のようになっているので、"田"に直す。
	- 修正ツールはこちら（Perl製）→ https://github.com/mghs15/gsi-vector-style-converter/blob/master/perl/replace_sprite.pl 

</details>

### 今回ためしに作成したもの

<table>
	<tr>
		<td><a href="https://mghs15.github.io/gsi-vector-style-converter/sstd.json">標準地図</a></td>
		<td><a href="https://mghs15.github.io/gsi-vector-style-converter/spale.json">淡色地図</a></td>
		<td><a href="https://mghs15.github.io/gsi-vector-style-converter/sblank.json">白地図</a></td>
	</tr>
	<tr>
		<td><a href="https://mghs15.github.io/gsi-vector-style-converter/sphoto.json">写真</a></td>
		<td><a href="https://mghs15.github.io/gsi-vector-style-converter/slabel.json">写真＋注記の注記</a></td>
		<td><a href="https://mghs15.github.io/gsi-vector-style-converter/sllabel.json">大きい注記の地図</a></td>
	</tr>
	<tr>
		<td><a href="https://mghs15.github.io/gsi-vector-style-converter/sstd2.json">標準地図2</a></td>
		<td><a href="https://mghs15.github.io/gsi-vector-style-converter/spale2.json">淡色地図2</a></td>
		<td><a href="https://mghs15.github.io/gsi-vector-style-converter/sblank2.json">白地図2</a></td>
	</tr>
</table>

※実装例（ https://mghs15.github.io/gsi-vector-style-converter/map.html ）で表示できるものです。

## ポリゴン塗り潰し（fill-pattern）用画像の出力

上記のツールでは、建物のハッチング等、[fill-pattern](https://docs.mapbox.com/mapbox-gl-js/style-spec/#paint-fill-fill-pattern)で指定する画像は表示されないので、spriteとは別にfill-pattern用の画像を準備する必要がある。

### fill-pattern用の画像をサイト側で都度生成する

fill-patternに指定されたIamgeのidが見つからないときに発火する`styleimagemissing`イベントを利用して、その都度必要なImageを追加する方法である。
地理院地図Vectorのハッチ用fill-patternに指定されているid名には、パターンや色の情報が含まれているため、ここから必要な画像を復元することができる。

利用方法としては、このレポジトリの`fill-pattern/addGsiHatchImage.js`を取り込んだうえで、以下のようなコードを記述する。

```
map.on('styleimagemissing', function (e) {
  
  var imgid = e.id;
  var hatchImg = convertGsiHatchImage(imgid);
  if(!hatchImg) return;
  
  map.addImage(imgid, { width: hatchImg.size, height: hatchImg.size, data: hatchImg.data });
  
});

```

※実装例（ https://mghs15.github.io/gsi-vector-style-converter/map.html ）では、この方法を用いている。


### fill-pattern用の画像を配列（Uint8Array）として出力する
<details>
<summary>fill-pattern用の画像を配列（Uint8Array）として出力し、その配列を取り込んで利用する方法</summary>

ハッチング用の小さな画像を大量に管理するのはコストがかかる。最終的に、[map.addImage](https://docs.mapbox.com/mapbox-gl-js/api/#map#addimage)を利用して、ハッチング用のパターンとして取り込むため、map.addImageが対応している`{width: number, height: number, data: Uint8Array}`の形式で出力することにした。

Mapbox GL JSでそのまま使えるJavascriptコードをコンソールログに出力するツールを作成した。

**ツールのURL**　https://mghs15.github.io/gsi-vector-style-converter/vhatch/

※アラートが出ますが、クエリ（URLの?以降の文字列）に何も指定していない限り、問題ありません。

出力形式は以下の通り。

（以下はひとつのパターンのみだが、これがロードされたstyle.jsonに含まれる数だけ出力される。）

```
map.addImage(
  "-gsibv-hatch-dot-4-20,90,255,1-", 
  {
    "width" : 4,
    "height" : 4,
    "data" : [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,20,90,255,255,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
  }
);
```
最終的に作成されたサンプルは以下の通り。結構地理院地図Vectorに近づいたと思います。
- [標準地図](https://mghs15.github.io/gsi-vector-mapbox-gl-js/std.html#14.01/35.44575/139.9552)
- [単色地図](https://mghs15.github.io/gsi-vector-mapbox-gl-js/pale.html#14.01/35.44575/139.9552)
- [白地図](https://mghs15.github.io/gsi-vector-mapbox-gl-js/blank.html#14.01/35.44575/139.9552)

※この3つのサンプルのレポジトリは[gsi-vector-mapbox-gl-js](https://github.com/mghs15/gsi-vector-mapbox-gl-js)です。

</details>

### fill-pattern用の画像をPNGとして出力する
<details>
<summary>PNG画像として出力し、その画像を取り込んで利用する方法</summary>

建物ハッチング等のパターン用の画像をPNGとして出力する必要がある場合の対応方法を紹介する。

#### fill-pattern用の画像の準備

地理院地図Vectorの内部処理で、fill-pattern用の画像が作成されているので、それらを出力するツールを作成した。

（上記のfill-pattern用の画像を配列（Uint8Array）として出力するツールと同じものですが、クエリに"?png"を指定すると、以下の通り、PNG画像として出力されます。）

自分個人用に使いことを考えているので、使い勝手はよくないのでご了承ください。
特に、**fill-pattern用の画像が作成されるたびに、別のウィンドウで開かれる**ので、利用する際は注意してほしい。
開いた無数のウィンドウからひとつずつ画像をダウンロードしていくことになります……。
（また、ブラウザのポップアップを許可しないとなりませんが、セキュリティ等、使用に伴うリスクは自己責任でお願いします。）

**ツールのURL**　https://mghs15.github.io/gsi-vector-style-converter/vhatch/?png

#### fill-pattern用の画像の読み込み

spriteファイルに入っていない画像を、Styleで利用したい場合、[map.loadImage](https://docs.mapbox.com/mapbox-gl-js/api/#map#loadimage)と[map.addImage](https://docs.mapbox.com/mapbox-gl-js/api/#map#addimage)を利用すればよい。

PNGが層をfill-patternに用いるサンプルは以下の通り。
- [標準地図](https://mghs15.github.io/gsi-vector-mapbox-gl-js-png/std.html#14.01/35.44575/139.9552)
- [単色地図](https://mghs15.github.io/gsi-vector-mapbox-gl-js-png/pale.html#14.01/35.44575/139.9552)
- [白地図](https://mghs15.github.io/gsi-vector-mapbox-gl-js-png/blank.html#14.01/35.44575/139.9552)

※この3つのサンプルのレポジトリは[こちら](https://github.com/mghs15/gsi-vector-mapbox-gl-js-png)

</details>

## 課題
1. 注記でHTMLが入っている場合、処理ができていない。
	- とりあえず、`<gsi-vertical>`タグを削除することにした。
	- `<gsi-vertical>`タグは、地理院地図Vectorでの縦書き表示のために設定されているようである。
	- 地理院地図Vectorではプラグインを利用して、データドリブンかつ日本語の縦書きを実現しているようである。
	- 一方、Mapbox GL JSでは、"text-writing-mode"で縦書きがサポートされたとはいえ、地理院地図Vectorでの表示を完璧に表現するのは難しそうである。→課題のさらなる整理は[こちら](https://github.com/mghs15/Note1/blob/master/mapbox-style.md#%E7%B8%A6%E6%9B%B8%E3%81%8D)
	- 地理院地図Vectorで利用される`<gsi-vertical>`タグを利用し、変換ツール側の処理で`<gsi-vertical>`タグが含まれているスタイルレイヤについては、縦書き用のスタイルレイヤと横書き用のスタイルレイヤに分割することで、疑似的にデータ内の属性値に応じた縦書き・横書きの書き分けができた。→実際に、 [変換ツール](https://mghs15.github.io/gsi-vector-style-converter/tool/)では、試験的に対応方法を実装。（ただし、長符「ー」の処理はMapbox GL JS自体の仕様なのでstyle.json側での対応は不可。）
	- 上記の縦書き用スタイルレイヤと横書き用スタイルレイヤの分割について、実際のstyle.jsonの実装では、"filter"に`["==", "arrng", 2]`（または、`["!=", "arrng", 2]`）に追加することで、レイヤ分けをすることとした。
2. 記号をspriteファイルからうまく取り出せていない。
	- "icon-image":"田"のように、アイコン名（sprite.jsonで定義されているもの）をシンプルにダブルクオーテーションで囲む形にすれば解決した。
	- どうやら、["sprite"](https://docs.mapbox.com/mapbox-gl-js/style-spec/#sprite)では、自動的に sprite@2x.png のようにScale Factorが付与されるらしい。これに対応していないと、スマホ等での閲覧に支障がある。
3. オリジナルの「MySimple.json」で、フォントファイルの読み込みがうまくいかない
	- ["text-font"](https://docs.mapbox.com/mapbox-gl-js/style-spec/#layout-symbol-text-font)（layout property）の設定が必要だったようだ。

## その他参考文献
* https://developer.mozilla.org/ja/docs/Web/API/HTMLCanvasElement/toBlob
* https://docs.mapbox.com/mapbox-gl-js/api/

