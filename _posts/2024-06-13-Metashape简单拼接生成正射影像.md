---
layout:     post
title:      Metashape简单拼接生成正射影像
subtitle:   
date:       2024-06-13
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Tansat
    - XCO2
    - Python
    - nc
---

### 问题
农业作物采样时，使用RGB相机拍摄了多张照片，需要拼接为一副图片

### 思路 
使用Metashape  

### 步骤
<font size=2 color=Red>本文使用了试用版(30天免费)，长期使用请购买正版</font>    

1、设置  
Tools-Preferences中设置GPU勾选

2、基本步骤  
对齐照片---> 建立密集点云---> 生成DEM ---> build orthomsaic---> export  orthomsaic


引用文献：  
[0] [PhotoScan集群，空三加密导入CC，正射影像生成及拼接（附航测练习数据）](https://zhuanlan.zhihu.com/p/574798988)  







 


