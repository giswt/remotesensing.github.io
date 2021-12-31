---
layout:     post
title:      Cesium加载TMS切片
subtitle:   
date:       2021-12-31
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Cesium  
    - TMS      
---

Cesium 可以加载来自ArcGIS、BingMap、GoogleMap、OSM等各种数据源，本文主要介绍加载TMS切片的功能。  
TMS的切片有不同的变种。我们使用QGIS自带的GDAL2TILES生成TMS切片。
### 步骤
###  1 生成切片 
打开QGIS的工具箱，目录GDAL--Raster miscellaneous--gdal2tiles  
![Tiles](http://www.spatial.pro/img/GDAL2TILES_QGIS.png)   
  打开生成切片的工具后，加载要切图的TIF文件，Tile cutting profile选择 Geodetic，Zoom level to render 输入要切图的等级 0-17，选择输出目录，然后点击运行，即可生成TMS切片备用，文件切片是会生成tilemapresource.xml文件，内容包含着切片的等级，内容如下。  
      
  ``` xml

  <?xml version="1.0" encoding="utf-8"?>
    <TileMap version="1.0.0" tilemapservice="http://tms.osgeo.org/1.0.0">
      <Title>Caofeidian_130209000000.tif</Title>
      <Abstract></Abstract>
      <SRS>EPSG:4326</SRS>
      <BoundingBox minx="118.13447884713771" miny="38.90996157442321" maxx="118.77860415054842" maxy="39.45711598074992"/>
      <Origin x="118.13447884713771" y="38.90996157442321"/>
      <TileFormat width="256" height="256" mime-type="image/png" extension="png"/>
      <TileSets profile="geodetic">
        <TileSet href="0" units-per-pixel="0.70312500000000" order="0"/>
        <TileSet href="1" units-per-pixel="0.35156250000000" order="1"/>
        <TileSet href="2" units-per-pixel="0.17578125000000" order="2"/>
        <TileSet href="3" units-per-pixel="0.08789062500000" order="3"/>
        <TileSet href="4" units-per-pixel="0.04394531250000" order="4"/>
        <TileSet href="5" units-per-pixel="0.02197265625000" order="5"/>
        <TileSet href="6" units-per-pixel="0.01098632812500" order="6"/>
        <TileSet href="7" units-per-pixel="0.00549316406250" order="7"/>
        <TileSet href="8" units-per-pixel="0.00274658203125" order="8"/>
        <TileSet href="9" units-per-pixel="0.00137329101562" order="9"/>
        <TileSet href="10" units-per-pixel="0.00068664550781" order="10"/>
        <TileSet href="11" units-per-pixel="0.00034332275391" order="11"/>
        <TileSet href="12" units-per-pixel="0.00017166137695" order="12"/>
        <TileSet href="13" units-per-pixel="0.00008583068848" order="13"/>
        <TileSet href="14" units-per-pixel="0.00004291534424" order="14"/>
        <TileSet href="15" units-per-pixel="0.00002145767212" order="15"/>
        <TileSet href="16" units-per-pixel="0.00001072883606" order="16"/>
        <TileSet href="17" units-per-pixel="0.00000536441803" order="17"/>
      </TileSets>
    </TileMap>
    
  ```
  <font color="#dd0000">注意：该工具生成的等级切片层级有差距，需要将文件夹命名数字减去1，比如名字为17的文件夹应该重命名为16才能在Cesium中加载</font>  

![Tiles1](http://www.spatial.pro/img/GDAL2TILES_AddData.png)  

### 2 Cesium加载Tiles
将上一步生成的切片文件夹所有文件和ilemapresource.xml文件放在Web服务器下，在html文件中编写代码调用。
核心代码如下：
``` Javascript
//添加TMS图层
  var layers = viewer.scene.imageryLayers;
  var tmsLayer = layers.addImageryProvider(
          new Cesium.TileMapServiceImageryProvider({
            url : './TMS_CFD',     //url为文件夹地址
          })
  );

```

全部代码文件：
``` html

<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Use correct character set. -->
  <meta charset="utf-8">
  <!-- Tell IE to use the latest, best version. -->
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <!-- Make the application on mobile take up the full browser screen and disable user scaling. -->
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">
  <title>Hello World!</title>
  <script src="../Cesium/Build/Cesium/Cesium.js"></script>
  <style>
    @import url(../Cesium/Build/Cesium/Widgets/widgets.css);
    html, body, #cesiumContainer {
      width: 100%; height: 100%; margin: 0; padding: 0; overflow: hidden;
    }
  </style>
</head>
<body>
<div id="cesiumContainer"></div>
<Script>
  Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJiYjNkZDBkNC0zNDdiLTRjOGMtODc0Yi1jM2RkZjAyODVjODMiLCJpZCI6MzM0MjQsImlhdCI6MTU5ODYxMjIwOX0.PbBBZpemi1z0pnqz8FPwejeF-GrciQqufZ7HI4Z4Wok';

  var viewer = new Cesium.Viewer('cesiumContainer', {
    imageryProvider : new Cesium.ArcGisMapServerImageryProvider({
      url : '//services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer'
    }),
    terrainProvider : Cesium.createWorldTerrain({
      requestVertexNormals: true
    }),
    baseLayerPicker : false,
    navigationHelpButton:false
  });

  viewer._cesiumWidget._creditContainer.style.display = "none";//去除版权信息

  viewer.animation.container.style.display ='none';//隐藏动画控件
  viewer.timeline.container.style.display ='none';//隐藏时间线控件
  viewer.geocoder.container.style.display ='none';//隐藏地名查找控件
  viewer.cesiumWidget.creditContainer.style.display ='none';//隐藏ceisum标识

  viewer.scene.camera.flyTo({
    destination:  { x: -5078350.364235281, y: 19789794.090862602, z: 13800250.389856659 },
    orientation: {
      heading: 6.022358768591566,
      pitch: -1.5685098881069806,
      roll: 0
    },
    duration: 3.0
  });

  //添加自定义按钮
  window.viewer = viewer;
  const toolbar = document.querySelector("div.cesium-viewer-toolbar");
  const modeButton = document.querySelector("span.cesium-sceneModePicker-wrapper");
  const myButton = document.createElement("button");
  myButton.classList.add("cesium-button", "cesium-toolbar-button");
  myButton.innerHTML = "X";
  toolbar.insertBefore(myButton, modeButton);

  //添加TMS图层
  var layers = viewer.scene.imageryLayers;
  var tmsLayer = layers.addImageryProvider(
          new Cesium.TileMapServiceImageryProvider({
            url : './TMS_CFD',     //url为文件夹地址
          })
  );

</Script>
</body>
</html>

```

加载后的效果图：  
![Tiles2](http://www.spatial.pro/img/CFD1_Cesium.png)  


### 引用  
[Cesium加载TMS切片](https://blog.csdn.net/u011365716/article/details/109038477)   
 [备用链接](https://cxymm.net/article/u011365716/109038477)
