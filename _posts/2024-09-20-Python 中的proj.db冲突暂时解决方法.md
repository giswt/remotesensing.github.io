---
layout:     post
title:      Python 中的proj.db冲突暂时解决方法
subtitle:   
date:       2024-09-20
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Python
    - proj
    - Anaconda       
---

### 问题
以前遇到rasterio与PostgreSQL中proj.db冲突的时候，我选择了卸载PostgreSQL，详见文章[Python 中的Rasterio与Postgresql冲突](https://www.spatial.pro/2023/11/10/Python-%E4%B8%AD%E7%9A%84Rasterio%E4%B8%8EPostgresql%E5%86%B2%E7%AA%81/)。文章中相当于删除了冲突的proj.db的版本。
但我们也不可能不用PostgreSQL数据库，怎么办呢？


### 思路 

上述原因是 proj.db 版本不同造成的。我同学告诉我Raterio新版本使用的投影库是3.0,很多早期的软件使用的2.0。  
我对比了不同的proj.db，发现表的数量和字段不一样，这是造成前面错误（no such column: area_of_use_auth_name）的原因。  
此外，windows操作系统的环境变量也是促成这一问题的原因之一（不清楚Mac和Linux是否有此问题）。Windows在安装Postgresql时写入了两个环境变量GDAL_DATA和PROJ_LIB，导致anaconda下python总是调用这两个变量指向的目录（旧的proj.db)。   
![GF1](http://www.spatial.pro/img/ODC_POSTGRESQL.png)

这是[Python 中的Rasterio与Postgresql冲突](https://www.spatial.pro/2023/11/10/Python-%E4%B8%AD%E7%9A%84Rasterio%E4%B8%8EPostgresql%E5%86%B2%E7%AA%81/)中的方案。  
           
              

我现在的思路是，把两个版本的proj.db都放在PROJ_LIB所在的目录下，保存为porj.db和proj1.db，如果程序调用失败，我就把proj.db先修改为proj2.db，把proj1.db修改为proj.db，使用PostgreSQL时再改回来。


 ```
import os
# 确保 PROJ_LIB 环境变量正确设置，指向包含 proj.db 文件的目录
# 查看当前设置
print(os.getenv('PROJ_LIB'))

# 如果没有设置或设置错误，你可以手动设置它（需要找到正确的路径）：
os.environ['PROJ_LIB'] = '/path/to/proj_lib'
 ```


