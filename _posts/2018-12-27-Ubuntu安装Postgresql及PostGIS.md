---
layout:     post
title:      2018-12-27-Ubuntu安装Postgresql及PostGIS
subtitle:   
date:       2018-12-27
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - postgres
    - postgis
    
---
### Postgresql 安装  
1.选择Ubuntud的版本号 Xenial(16.04)

2.在目录/etc/apt/sources.list.d下创建 pgdg.list

3.将下列内容添加到文件中

```
deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main
``` 
4.在命令行下运行以下命令
```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
```
5.安装postresql
```
sudo apt install postgresql-10
```
### Postgis 安装
1. 安装PostGIS
```
sudo apt install postgresql-10-postgis-2.4 
sudo apt install postgresql-10-postgis-scripts

#to get the commandline tools shp2pgsql, raster2pgsql you need to do this
sudo apt install postgis
``` 
2. 添加相关模块
```
# Install pgRouting 2.6 package 
sudo apt install postgresql-10-pgrouting

sudo -u postgres psql
CREATE EXTENSION adminpack;

\connect postgres;


CREATE EXTENSION postgis ;
SELECT postgis_full_version();
```
3.修改用户名和密码
```
sudo -u postgres psql
alter user postgres with password '111111'
```


### 其他资料
```
#卸载软件及其配置文件
apt-get --pure remove <package>
# 删除没用的依赖包
apt-get autoremove <package
```


摘自：
	  [Postgresql](https://www.postgresql.org/download/linux/ubuntu/)  
      [PostGIS](http://trac.osgeo.org/postgis/wiki/UsersWikiPostGIS24UbuntuPGSQL10Apt)  
	   



  
  
  
