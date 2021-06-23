# plateau-tokyo23ku-building-mvt-2020

[3D都市モデル（Project PLATEAU）東京都23区（CityGML 2020年度）](https://www.geospatial.jp/ckan/dataset/plateau-tokyo23ku-citygml-2020) で
公開されている CityGML データのうち、建築物 (bldg:Building) について Mapbox Vector Tile 形式に変換したデータセットです。

# デモ

## index.html

データの確認を目的としたデモです。建物をクリックすることで属性情報がポップアップ表示されます。

<https://indigo-lab.github.io/plateau-tokyo23ku-building-mvt-2020/>

[![plateau-tokyo23ku-building-mvt-2020](https://repository-images.githubusercontent.com/357730702/111d3b00-c2f0-11eb-9899-7658407cfcee)](https://indigo-lab.github.io/plateau-tokyo23ku-building-mvt-2020/)

## example-gsibv.html

[地理院地図Vector](https://github.com/gsi-cyberjapan/gsimaps-vector-experiment) を併用したデモです。

<https://indigo-lab.github.io/plateau-tokyo23ku-building-mvt-2020/example-gsibv.html>

[![example-gsibv.html](https://user-images.githubusercontent.com/8913051/120594099-63d72e00-c47b-11eb-82fe-13d2157f8e20.jpg)](https://indigo-lab.github.io/plateau-tokyo23ku-building-mvt-2020/example-gsibv.html)

# タイル仕様

URL         | <https://indigo-lab.github.io/plateau-tokyo23ku-building-mvt-2020/{z}/{x}/{y}.pbf>
----------- | -----------------------------------------------------------------------------------
データソース  | [3D都市モデル（Project PLATEAU）東京都23区（CityGML 2020年度）](https://www.geospatial.jp/ckan/dataset/plateau-tokyo23ku-citygml-2020)
ズームレベル  | 10〜16 (※)
作成日時     | 2021年4月22日

※ 10〜15のズームレベルでは個々の pbf ファイルが 500kbytes 以内に収まるように、比較的小さな建築物を間引く処理が行われています


## レイヤー定義

以下のレイヤーを含んでいます。

source-layer | description
------------ | --------------------------------------------
bldg         | 建築物 (bldg:Building) を収納したレイヤー


## ジオメトリ情報

ジオメトリ情報は、ソースの CityGML の 屋根外形 (bldg:lod0RoofEdge) の情報を元に GeoJSON Polygon を生成して得たものです。
LOD0 (地形モデルレベル) の情報であることに注意してください。

また、ズームレベルに応じた座標値の丸めが行われていることにも注意してください。

## 属性情報

属性情報は、ソースの CityGML の汎用属性 (gen:value) および計測高さ(bldg:measuredHeight) を抽出して
以下のようなルールで付与したものです。

ソースの CityGML が以下のようなデータ構造であった場合、

```xml
<bldg:Building gml:id="BLD_26482c83-174c-4892-8899-e1574353a3cf">
  <gen:stringAttribute name="建物ID">
    <gen:value>13108-bldg-154</gen:value>
  </gen:stringAttribute>
  <gen:stringAttribute name="大字・町コード">
    <gen:value>44</gen:value>
  </gen:stringAttribute>
  <gen:stringAttribute name="町・丁目コード">
    <gen:value>3</gen:value>
  </gen:stringAttribute>
  <gen:stringAttribute name="13_区市町村コード_大字・町コード_町・丁目コード">
    <gen:value>13108044003</gen:value>
  </gen:stringAttribute>
  <gen:genericAttributeSet name="江東内部河川流域洪水浸水想定区域（想定最大規模）">
    <gen:stringAttribute name="規模">
      <gen:value>L2</gen:value>
    </gen:stringAttribute>
    <gen:stringAttribute name="浸水ランク">
      <gen:value>1</gen:value>
    </gen:stringAttribute>
    <gen:measureAttribute name="浸水深">
      <gen:value uom="m">0.437</gen:value>
    </gen:measureAttribute>
  </gen:genericAttributeSet>
  <bldg:measuredHeight uom="m">4.9</bldg:measuredHeight>
  <bldg:lod0RoofEdge>
  ...
  </bldg:lod0RoofEdge>
</bldg:Building>
```

以下のような GeoJSON properties が構成されます。

```json
{
  "type" : "Feature",
  "geometry" : {
    "type" : "Polygon",
    "coordinates" : []
  },
  "properties" : {
    "建物ID" : "13108-bldg-154",
    "大字・町コード" : "44",
    "町・丁目コード" : "3",
    "13_区市町村コード_大字・町コード_町・丁目コード" : "13108044003",
    "江東内部河川流域洪水浸水想定区域（想定最大規模）>規模" : "L2",
    "江東内部河川流域洪水浸水想定区域（想定最大規模）>浸水ランク" : "1",
    "江東内部河川流域洪水浸水想定区域（想定最大規模）>浸水深" : 0.437,
    "measuredHeight" : 4.9
  }
}
```

- gen:value に対応するプロパティ名は、祖先の gen:* 要素に付与された name 属性を > で結合したものです
- bldg:measuredHeight に対応するプロパティ名は measuredHeight です


# ライセンス

本データセットは [CC-BY-4.0](LICENSE) で提供されます。
使用の際にはこのレポジトリへのリンクを提示してください。

また、本データセットは [3D都市モデル（Project PLATEAU）東京都23区（CityGML 2020年度）](https://www.geospatial.jp/ckan/dataset/plateau-tokyo23ku-citygml-2020) を
加工して作成したものです。
本データセットの使用・加工にあたっては、[PLATEAU Policy](https://www.mlit.go.jp/plateau/site-policy/) を確認し、権利者の権利を侵害しないように留意してください。

# 技術情報

## tippecanoe

本データセットは CityGML から独自に生成した GeoJSON Text Sequences を [tippecanoe](https://github.com/mapbox/tippecanoe) で MVT に変換したものです。

tippecanoe に設定したオプションは以下の通りです。

```sh
$ bzcat geojson.bz2 | tippecanoe --no-tile-compression -ad -an -Z10 -z16 -e dist -l bldg -ai
```

## maplibre-gl-js

デモページでの MVT の表示には [maplibre-gl-js](https://github.com/maplibre/maplibre-gl-js) を使っています。

# 更新履歴

## 2021-06-02

- 初版公開

## 2021-06-23

- CityGML においてプロパティの値が明示的に数値である場合、MVT では string として float としてエンコードされるように変更
- uro:buildingRoofEdgeArea の値をプロパティとして保持するように変更
- uro:buildingRoofEdgeArea が設定されていない場合はジオメトリから計算して補完
