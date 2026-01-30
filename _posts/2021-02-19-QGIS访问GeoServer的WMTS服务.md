---
layout:     post
title:      QGIS访问GeoServer的WMTS服务
subtitle:   
date:       2021-02-19
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - QGIS  
    - GeoServer  
    - WMTS    
---
     
步骤：  
###  1 Geoserver发布WMTS服务 
<font color="#dd0000">tiff数据文件命名建议为英文，否则可能会出现无法读取的错误</font>   
进入geoserver后，点击工作区，然后选择“添加新的工作区”，添加工作区后，添加数据存储，导入要发布的tiff影像。

在左侧“数据”栏中选择“图层”,然后点击“发布的图层名称”跳转到图层编辑页面,切换到然后“Tile Caching”一栏中，选择第一行的复选框后会加载一些其他数据，点击“保存”。

在左侧“Tile Caching”栏中选择“Tile Layers”，找到发步的图层组名称所在的那一行，点击“Send/Truncate”，跳转到”GeoWebCache“编辑页面，按照说明编辑数据后，点”submit“提交，跳转到一个显示WMTS状态信息的新页面，点击”Refresh LIst“刷新列表可以查看切片的进度。  
![wmts2](http://www.spatial.pro/img/QGIS_GeoServer_GeoWebCache.png)


### 2 QGIS 访问WMTS服务
打开QGIS，新建WMS/WMTS，输入新建链接的名字和WMTS服务的url(http://10.1.22.76:8080/geoserver/gwc/service/wmts?request=getcapabilities),点击OK即可获取该Geoserver对应的WMTS服务。

![wmts2](http://www.spatial.pro/img/QGIS_GeoServer_WMTS.png)

### 引用  
[geoserver发布wms服务和wmts服务，并在cesium上进行调用](https://blog.csdn.net/weixin_43311389/article/details/99979275)  
[Re: [Geoserver-users] Accessing GeoServer WMTS service via ArcMap](https://sourceforge.net/p/geoserver/mailman/message/36377216/)  
[使用GeoServer发布WMTS](https://blog.csdn.net/weixin_38843590/article/details/79879317)
