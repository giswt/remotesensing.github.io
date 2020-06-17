---
layout:     post
title:      ImportError: DLL load failed: 操作系统无法运行
subtitle:   
date:       2020-06-09
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Ubuntu
    
---


### 前因 ###

Python版本：Anaconda3
在pycharm中加载GDAL 等包运行不起来,提示: "ImportError: DLL load failed: 操作系统无法运行 %1。"



  
### 步骤 ###

解决方案：

将\Anaconda3\Library\bin目录中的libeay32.dll和ssleay32.dll放到“C:\\WINDOWS\SYSTEM32\"下面，替换原来的文件就行了。

引用文献：[大卫德2015](https://zhuanlan.zhihu.com/p/32863888)  