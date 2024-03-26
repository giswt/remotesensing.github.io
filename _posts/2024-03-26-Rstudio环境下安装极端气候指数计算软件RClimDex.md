---
layout:     post
title:      Rstudio环境下安装极端气候指数计算软件RClimDex
subtitle:   
date:       2024-03-25
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - R
    - RClimDex
    - RStudio    
---

### 问题
在Windows和Rstudio环境下安装极端气候指数计算软件RClimDex


### 思路 
按照[官方2018年文档](https://github.com/ECCC-CDAS/RClimDex/blob/master/inst/doc/manual.pdf)的说明,RClimDex依赖的R包包括[climdex.pcic(Version 1.1-6)](https://cran.r-project.org/src/contrib/Archive/climdex.pcic/) 和PCICt (Version0.5-4)。实际上在安装过程中还需要'caTools'和 'Rcpp' 两个包，这两个包需要在climdex.pcic前安装。

### 步骤
<font size=2 color=Red>运行环境：RStudio/2023.12.0+369  R version 4.3.2 (2023-10-31 ucrt) </font>  

####  下载RTOOLS
RClimDex 依赖的包有些很旧，已经被官方剔除，需要自己下载编译，因此需要下载RTOOLS。选择 [国内清华镜像](https://mirrors.tuna.tsinghua.edu.cn/CRAN/),然后选择Download R for Windows，再选择RTools,选择RStudio中R版本对应的RTools,然后安装。

#### 安装其他包
```
> install.packages('PCICt')

```
下载[climdex.pcic源文件](https://cran.r-project.org/src/contrib/Archive/climdex.pcic/)，版本为climdex.pcic_1.1-6.tar.gz，下载后放置在我的文档根目录（RStudio的当前目录）。
```
> install.packages("caTools")
> install.packages("Rcpp")
> install.packages("~/climdex.pcic_1.1-6.tar.gz", repos = NULL, type = "source")
```
下载[RClimDex1.9-3](https://github.com/ECCC-CDAS/RClimDex/releases)，下载后放置在我的文档根目录（RStudio的当前目录）。
```
> install.packages("~/RClimDex-1.9-3.tar.gz", repos = NULL, type = "source")
```
#### 启动RClimDex

```
> library(PCICt)
> library(climdex.pcic)
> library(RClimDex)
> rclimdex.start()
```
![run1](http://www.spatial.pro/img/RClimDex.png)  

引用文献：
[0] [GITHUB RClimDex](https://github.com/ECCC-CDAS/RClimDex)
    [RClimDex Manual](http://www.spatial.pro/files/manual.pdf)
[1] [Rstudio环境下安装极端气候指数计算软件RClimDex](https://blog.csdn.net/m0_57847669/article/details/134364501)  
[2] [Rstudio多个版本R语言环境进行选择](https://blog.csdn.net/faith_mo_blog/article/details/52192105)      
[3] [R语言学习（1）——Windows系统下安装R及Rtools](https://blog.csdn.net/liukaiyue99/article/details/131470413)  


 


