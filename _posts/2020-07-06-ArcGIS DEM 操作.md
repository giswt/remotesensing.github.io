---
layout:     post
title:      ArcGIS DEM 操作
subtitle:   
date:       2020-07-06
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - DEM
    - GIS
    - ArcGIS  
---
### 12.5m DEM 下载 ###

1. 注册 NASA用户 https://urs.earthdata.nasa.gov/
2. 在浏览器中打开 https://search.asf.alaska.edu/
3. 在Dataset中选择ALOS PALSAR
4. 使用框选工具，选择你要下载的区域
5. 过滤条件在Beam Mode中选择 FBS FBD（或File Type中直接选择Hi-Res Terrian Corrected）
6. 点击搜索，获取DEM列表
7. 下载数据。压缩文件中.dem.tif为DEM数据
关于 12.5米DEM色说明：
实际上以下下载的数据是用于Alos几何校正的地形数据，会有数据缺失（Nodata）
原文：Judging from the filenames in the screen provided, you are not using ALOS DEM but SRTM data that accompany ASF ALOS RTC products. Despite a widespread misconception, those are not ALOS DEM but up-sampled SRTM elevation tiles, 30m original resolution, used for terrain correction in ALOS radar images by the ASF. 12.5m resolution is only for the product raster file not original data. https://media.asf.alaska.edu/uploads/RTC/rtc_product_guide_v1.2.pdf


### DEM 拼接 ###

使用工具：Toolbox->Data Management Tools->Rastrer->Raster Dataset->MosaicToNewRaster

参数：
    Pixel Type：16_BIT_SIGNED
	Number of Bands: 1 (DEM 只有一个波段)
  
### DEM数据中无数据点的插值处理 ###
使用工具：Toolbox->Spatial Analyst Tools-> Map Algebra->Raster Calculator
公式：Con(IsNull("Tangshan2011.tif"), Int(FocalStatistics("Tangshan2011.tif", NbrRectangle(5,5, "CELL"), "MEAN")), "Tangshan2011.tif")
FocalStatistics 在10.0以后的版本中替代了focalmean。 将"Tangshan2011.tif"更改为自己的文件，如果一次计算后仍有无数据点，多计算几次直到没有无数据点值。


### DEM数据中无数据点的插值处理 ###

引用文献：[The Geo-ICT Blog](https://thegeoict.com/blog/2019/10/28/downloading-12-5-m-alos-palsar-high-resolution-dem/) 
          [GIS前沿](https://mp.weixin.qq.com/s/CO-_blwzUXCpcgqlv-nnBA) 
		  [Sotiris Valkaniotis](https://gis.stackexchange.com/questions/271053/alos-dem-shows-continious-no-data-how-to-over-come)

          [小安](https://www.zhihu.com/question/30188487)  
          [Jolin-King的博客](http://blog.sina.com.cn/s/blog_68b62bb50100vzyb.html)
          [ESRI社区](https://community.esri.com/thread/9037) 		  
