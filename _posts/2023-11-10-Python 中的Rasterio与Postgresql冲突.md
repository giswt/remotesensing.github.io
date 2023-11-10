---
layout:     post
title:      Python 中的Rasterio与Postgresql冲突
subtitle:   
date:       2023-11-10
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Python
    - Rasterio
    - EPSG     
---

### 问题
Anaconda中的Rasterio使用过程中无法获取遥感影像的crs。  
具体错误如下：  
CRSError: The EPSG code is unknown. PROJ: proj_create_from_database: 
SQLite error on SELECT name, coordinate_system_auth_name, coordinate_system_code, 
geodetic_crs_auth_name, geodetic_crs_code, conversion_auth_name, conversion_code, 
area_of_use_auth_name, area_of_use_code, text_definition, deprecated FROM projected_crs 
WHERE auth_name = ? AND code = ?: no such column: area_of_use_auth_name  
![GF1](http://www.spatial.pro/img/ODC_Sentinel2totif.png)


### 思路 

上述原因是 proj.db 版本不同造成的。我同学告诉我Raterio新版本使用的投影库是3.0,很多早期的软件使用的2.0。  
我对比了不同的proj.db，发现表的数量和字段不一样，这是造成前面错误（no such column: area_of_use_auth_name）的原因。  
此外，windows操作系统的环境变量也是促成这一问题的原因之一（不清楚Mac和Linux是否有此问题）。Windows在安装Postgresql时写入了两个环境变量GDAL_DATA和PROJ_LIB，导致anaconda下python总是调用这两个变量指向的目录（旧的proj.db)。   
![GF1](http://www.spatial.pro/img/ODC_POSTGRESQL.png)

### 解决方案
卸载Postgresql，但是卸载后这个proj.db仍然存在，需要手动删除。环境变量我没有修改，仍然指向一个错误的目录（但木已经不存在），不过程序正常了。  
为大家提供一个思路，可能每个人的问题不同。  

 


