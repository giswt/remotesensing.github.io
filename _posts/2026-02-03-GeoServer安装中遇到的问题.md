---
layout:     post
title:      GeoServer安装中遇到的问题
subtitle:   
date:       2026-02-03
author:     WT
header-img: img/DJI_0294.JPG
catalog: true
tags:
    - GeoServer
    - Java        
---  
## 背景

安装GeoServer 2.82时提示需要安装Java 17或者21，使用Oracle官方的JDK17 默认安装不带JRE，Geoserver监测不到Java环境。


### 解决方法
不管怎么设置Java环境，重装都不行，即使按照他人的方法，用命令行装了JRE还是不行，最后使用了在GeoServer安装页面上的那个Java链接下载了对应的文件，然后安装成功了。顺便发布了TIFF的服务。


参考文献：  
[GeoServer发布tiff格式地图并进行切片缓存](https://blog.51cto.com/u_13560480/2542960)









