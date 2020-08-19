---
layout:     post
title:      QGIS：将Geotiff转换为MBTILES
subtitle:   
date:       2020-08-14
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - QGIS
    - MBTILES
    
---


插件QTiles可以将用户加载到QGIS的数据转为MBTILES格式，包括但不仅限于GeoTIFF。
操作相对简单，只需要设置输出的文件或者目录，范围和Tiles的级数即可完成转换。此外，还可以设置tile size之类的参数。
比gdal_translate易用。


![GF2](http://www.spatial.pro/img/QTiles.png)

Qtiles 只能安装在2.x版本的QGIS中（本人实验成功），有人说3.0的可以通过Processing > Processing Toolbox > Raster tools > Generate YYZ Tiles生成MBTiles文件。

引自[Youtube](https://www.youtube.com/watch?v=G_d8BN2pHLE)
[QTiles replacement for QGIS3](https://gis.stackexchange.com/questions/353274/qtiles-replacement-for-qgis3)



