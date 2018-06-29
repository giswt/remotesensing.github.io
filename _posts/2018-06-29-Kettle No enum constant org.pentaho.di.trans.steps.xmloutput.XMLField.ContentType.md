---
layout:     post
title:      Kettle No enum constant org.pentaho.di.trans.steps.xmloutput.XMLField.ContentType
subtitle:   
date:       2018-6-29
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Kettle
---
### 问题 ###
	Kettle在执行CSV转换为XML时出现 No enum constant org.pentaho.di.trans.steps.xmloutput.XMLField.ContentType
### 解决方案 ###

  原因没有选择输出字段作为xml哪部分输出，需要指定输出的是 attribute 还是element.
 ![GF2](http://www.spatial.pro/img/kettlexmlerror.png)
 
 引自[CSDN,MichaelJY1991](https://blog.csdn.net/michaeljy1991/article/details/48521761)
