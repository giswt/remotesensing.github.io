---
layout:     post
title:      在Ubuntu上安装GeoServer
subtitle:   
date:       2020-09-03
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Ubuntu
    - GeoServer  
---

  本文主要介绍在Ubuntu系统下安装GeoServer,并修改文件使其能够通过systemctl命令运行。    
  本文主要是对 [Hillson Ghimire](https://medium.com/@ghimirehillson/installing-geoserver-on-linux-ubuntu-18-04-mint-d5f54d70ff32)文章的翻译，但他的文章代码缺少了一部分，无法启动，根据[Github](https://gist.github.com/iacovlev-pavel/9006ba2f33cafc8d2ab71528f25f9f45)上的内容进行了补充。  
步骤：  
### 1、更新升级ubuntu 
```
$ sudo apt-get update
$ sudo apt-get upgrade
```
### 2、下载 GeoServer  
推荐使用最新的 stable 版本 : http://geoserver.org/release/stable/
，下载Platform Independent Binary 格式的文件到本地/Downloads/文件夹。
### 3、安装 Java Runtime Environment
GeoServer 运行需要JRE环境，使用以下命令判断使用安装了Java：
```
$ java -version
```
如果没有安装，使用以下命令安装：
```
$ sudo apt install openjdk-8-jdk
```
### 4、复制文件到指定位置
运行以下命令：
```
$ sudo mkdir /usr/share/geoserver
$ sudo apt install unzip
$ cd /home/&USER/Downloads/

#将&USER 替换为你的用户名
$ sudo unzip ./geoserver-2.XX.X-bin.zip -d /usr/share/geoserver
$ cd /usr/share/geoserver/
$ sudo mv ./geoserver-2.XX.X/* ./

#改变文件夹权限
$ sudo chown -R $USER /usr/share/geoserver
$ cd /usr/share/geoserver/bin
$ ./startup.sh
```
上述命令运行后GeoServer就启动了，在浏览器中输入http://localhost:8080,可以看到界面了。默认用户名：admin，默认密码：geoserver

### 5、编辑启动/停止脚本: init.d
搜索GeoServer Linux init Script，找到[脚本](https://docs.geoserver.org/latest/en/user/production/linuxscript.html)下载到本地/Downloads/文件夹中，将文件内容复制到/etc/init.d/geoserver文件中，保存文件。  
```
$ sudo vim /etc/init.d/geoserver
```
<font color="#dd0000">如果上述文件中没有以下内容，需要添加</font>
```
SERVICE_NAME=geoserver
GEOSERVER_HOME=/path/to/geoserver
START=$GEOSERVER_HOME/bin/startup.sh
STOP=$GEOSERVER_HOME/bin/shutdown.sh
RUNAS=root
JAVA_HOME=/usr/lib/jvm/java-13-openjdk-amd64 # or something else

PIDFILE=/var/run/geoserver.pid
LOGFILE=/var/log/geoserver.log
```

创建/etc/default/geoserver文件，
```
sudo vim /etc/default/geoserver
```
加入以下内容
```
USER=geoserver 
GEOSERVER_DATA_DIR=/usr/share/geoserver/data_dir
GEOSERVER_HOME=//usr/share/geoserver
JAVA_HOME=/usr/lib/jvm/java-6-sun
JAVA_OPTS="-Xms128m -Xmx512m"
```
修改USER、GEOSERVER_DATA_DIR、GEOSERVER_HOME和JAVA_HOME的值，已匹配自己的环境。

#### 如何查看JAVA_HOME
```
$ cd /usr/lib/jvm/
$ ls
```
### 6、改变用户模式
```
$ sudo chmod +x /etc/init.d/geoserver
#Reload Daemon
$ sudo systemctl daemon-reload
```
启动服务localhost:8080,
```
$ sudo systemctl start geoserver
$ sudo systemctl status geoserver
```
停止服务,
```
$ sudo systemctl stop geoserver
```
至此，完成了GeoServer的安装。




##### 引用  
[1 主要参考](https://medium.com/@ghimirehillson/installing-geoserver-on-linux-ubuntu-18-04-mint-d5f54d70ff32)  
[2 次要参考](https://gist.github.com/iacovlev-pavel/9006ba2f33cafc8d2ab71528f25f9f45)  
[3 官方说明](https://docs.geoserver.org/latest/en/user/production/linuxscript.html)   
