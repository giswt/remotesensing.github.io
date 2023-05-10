---
layout:     post
title:      MacOS下使用Anaconda和Pycharm配置R环境
subtitle:   
date:       2023-05-04
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Mac
    - Anaconda
    - Pycharm  
    - R 
---

### 安装前提
1、MacOS 10.15+(Intel) 
2、Anaconda  
3、PyCharm 2022.3

### Anaconda 安装R环境
Pycharm下的R插件 R Language for IntelliJ 只支持R版本大于3.4的，Anaconda默认安装3.3版本的R，所以使用conda强制安装高版本的R。  
通过以下命令可以在Anaconda中创建r4-base的虚拟环境，并安装R-4.2.3版本
```
conda create --name r4-base
conda activate r4-base
conda install -c conda-forge r-base
conda install -c conda-forge/label/gcc7 r-base
```
![anaconda1_R](http://www.spatial.pro/img/anaconda1_R.png)  

#### Pycharm 安装 R Language for IntelliJ
1、 打开Pycharm，打开 Perferences--> Plugins，输入R搜索，找到R Language for IntelliJ安装。  
![anaconda2_R](http://www.spatial.pro/img/anaconda2_R.png)    

#### 新建工程，设置R环境
1、新建工程，选择R环境，添加R环境过程中可能看不到Anaconda所在的安装目录opt，此时按下Command + Shift + .三个键，即可看到隐藏的文件夹，选择/opt/anaconda3/envs/r4-base/bin下的R，即可加载R环境。
![anaconda3_R](http://www.spatial.pro/img/anaconda3_R.png)  

引用文献：  
    [【R语言】环境配置Anaconda + R4.1.3 + Pycharm](https://blog.csdn.net/qq_43426078/article/details/124912611)    
    [Anaconda配置镜像源](https://blog.csdn.net/qq_32650831/article/details/127952502)   
    [Show hidden files by default in Mac file chooser](https://youtrack.jetbrains.com/issue/IDEA-91807)  
         
         	 
