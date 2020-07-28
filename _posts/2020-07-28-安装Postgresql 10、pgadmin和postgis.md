---
layout:     post
title:      安装Postgresql 10、pgadmin和postgis
subtitle:   
date:       2020-07-28
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Postgresql
	- PostGIS
	
 
---
### 安装 Postgresql 10 ###
```
sudo apt update
sudo apt-get install postgresql-10 postgresql-client-10 postgresql-contrib-10 libhdf5-serial-dev postgresql-doc-10

```
配置/etc/postgresql/10/main/pg_hba.conf文件，修改local的权限（peermd5）。
```
sudo service postgresql restart

sudo -u postgres createuser --superuser dc_user  #用户名和下一行的密码可以自定义
sudo -u postgres psql -c "ALTER USER dc_user WITH PASSWORD 'localuser1234';"
createdb -U dc_user gisdb #创建数据库
```

### 安装pgadmin ###
```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
sudo apt install pgadmin4 pgadmin4-apache2

```
安装过程中会要求输入初始的Email用于创建管理员，默认值即可，密码自定义。

### 安装PostGIS ###
首先使用以下代码安装，如果有问题（libgdal20库缺失之类的错误），则按照第二种方法安装。
方法1
```
sudo apt install postgresql-10-postgis-2.4
sudo apt install postgresql-10-postgis-scripts
```
方法2
```
sudo add-apt-repository ppa:ubuntugis/ppa
sudo apt install postgis
```
使用pgadmin连接到数据库，右键需要开启PostGIS支持的数据库，输入以下代码
```
CREATE EXTENSION postgis SCHEMA public;
```
运行成功则启用成功，你会发现 extension 中多了postgis项，tables 中多了spatial_ref_sys



引用文献：
         [Hitesh Jethva](https://www.atlantic.net/vps-hosting/how-to-install-and-use-pgadmin-on-ubuntu-18-04/)
         [stackexchange](https://stackoverflow.com/questions/58239607/pgadmin-package-pgadmin4-has-no-installation-candidate)
         [kitcharoenp](https://kitcharoenp.github.io/postgresql/postgis/2018/05/28/set_up_postgreSQL_postgis.html)
         [stackexchange](https://gis.stackexchange.com/questions/318276/installing-postgis-after-postgresql-ubuntu-18-04)		 
         