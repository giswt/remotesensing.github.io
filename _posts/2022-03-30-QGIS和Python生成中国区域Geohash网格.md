---
layout:     post
title:      QGIS和Python生成中国区域Geohash网格
subtitle:   
date:       2022-03-30
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - QGIS
    - Python  
    - Geohash 
---

### 步骤
###  1 获取中国地图，利用QGIS绘制包含中国全境的简单多边形。
 
![Tiles](http://www.spatial.pro/img/China.png)   

### 2 安装Python库
![Tiles](http://www.spatial.pro/img/Installpolygeohasher.png) 
Installpolygeohasher.png
### 3 按需求生成Geohash网格
```python
from polygeohasher import polygeohasher
import geopandas as gpd
import matplotlib.pyplot as plt
%matplotlib inline
gdf=gpd.read_file("/Users/tuo/Data/China_Geohash.geojson")

#returns a dataframe with list of geohashes for each geometry
df_3 = polygeohasher.create_geohash_list(gdf, 3,inner=False) 

geo_df = polygeohasher.geohashes_to_geometry(df_3,"geohash_list") 
geo_df.to_file("China_Geohash_3.geojson",driver = "GeoJSON") 
```
### 4 QGIS加载Geohash网格和中国边界线

 
![China_geohash_3](http://www.spatial.pro/img/China_geohash_3.png)  



### 引用  
[Polygeohasher: an optimized way to create geohashes](https://www.geospatialworld.net/blogs/polygeohasher-an-optimized-way-to-create-geohashes/)   

