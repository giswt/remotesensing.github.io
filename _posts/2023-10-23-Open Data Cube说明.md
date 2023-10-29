---
layout:     post
title:      Open Data Cube 说明
subtitle:   
date:       2023-10-23
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Open Data Cube  
    - ODC      
---
本文记录 2023年Open Data Cube 安装过程及说明  
<font color="#dd0000">datacube 1.8.17.dev2+g0cfe300d.d20231027</font>    
  
###  1 Install the Open Data Cube Software  ODC 核心安装

ODC8.16  以后的版本要求python 3.10
建议直接安装Ubuntu 22，它自带的版本是Pyhon 3.10.12



按照[Ubuntu Developer Setup](https://opendatacube.readthedocs.io/en/latest/installation/setup/ubuntu.html)的方法进行安装。
#### 1.1 mamba 安装
按照上面的安装步骤，需要使用mamba安装ODC所需的核心环境。使用mamba需要安装Miniforge。
github上的安装命令如下：
```
curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
bash Miniforge3-$(uname)-$(uname -m).sh
```
或者
```
wget "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
bash Miniforge3-$(uname)-$(uname -m).sh
```
中国的网络访问github会有间歇性断流，可以直接在GITHUB上下载，再运行bash命令安装
安装完的配置工作最好在虚拟环境下完成

### 2 S2 indexing

按照github上的内容[Indexing Sentinel-2 Cloud-Optimised GeoTIFFs](https://github.com/opendatacube/datacube-dataset-config/blob/main/sentinel-2-l2a-cogs.md)进行操作即可。



### 3 其他常用应用

#### 3.1 Python
显示所有已存在的虚拟环境列  
```
conda info --envs
```

列出所有安装在该虚拟环境中的包名和版本号
```
conda list -n <env_name>
```

列出所有已存在的虚拟环境列表，并附带其路径信息
```
conda env list
```



#### 3.2 PostgresQL
直接在官方网站找安装说明，比什么都靠谱。  
```
sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql
```

#### 3.3 pgadmin4
直接在官方网站找安装说明，比什么都靠谱。  
此外，pgadmin4 分为web端和desktop端，可以安装两个，也可以只安装一个。
```
#
# Setup the repository
#

# Install the public key for the repository (if not done previously):
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

# Create the repository configuration file:
sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'

#
# Install pgAdmin
#

# Install for both desktop and web modes:
sudo apt install pgadmin4

# Install for desktop mode only:
sudo apt install pgadmin4-desktop

# Install for web mode only: 
sudo apt install pgadmin4-web 

# Configure the webserver, if you installed pgadmin4-web:
sudo /usr/pgadmin4/bin/setup-web.sh
```
#### 3.4 Jupyter notebook 
直接在官方网站找安装说明,比什么都靠谱。  
```
pip install notebook
```




### 4 可能出现的错误

<font color="#dd0000">ERROR: Could not build wheels for aiohttp, which is required to install pyproject.toml-based</font>   
造成这一原因的问题是虚拟环境中安装了Python 3.12 AOIHTTP 包不兼容，可以将Python降低到3.10，也可以在3.12环境中安装
Basically, Python 3.12 is not supported yet in regular releases. For Python 3.12 install the beta 3.9.0b0.
```
pip install aiohttp==3.9.0b0
```
<font color="#dd0000">ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
datacube 1.8.17.dev2+g0cfe300d.d20231027 requires jsonschema<4.18, but you have jsonschema 4.19.1 which is incompatible.


  Attempting uninstall: jsonschema
    Found existing installation: jsonschema 4.17.3
    Uninstalling jsonschema-4.17.3:
      Successfully uninstalled jsonschema-4.17.3</font> 
  
<font color="#dd0000">ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
jupyter-events 0.8.0 requires jsonschema[format-nongpl]>=4.18.0, but you have jsonschema 4.17.3 which is incompatible.
jupyterlab-server 2.25.0 requires jsonschema>=4.18.0, but you have jsonschema 4.17.3 which is incompatible.</font>

上述问题是jupyter notebook 中使用的 jsonschema 和 Open datacube中使用的jsonschema版本不一致造成的，解决方案是降低jupyter notebook的版本，安装6.4.8（Jupyter 7 以上的jsonschema版本高）
```
pip uninstall notebook
pip install notebook==6.4.8
```

<font color="#dd0000">TypeError: warn() missing 1 required keyword-only argument: 'stacklevel'</font>   
<font color="#dd0000">ModuleNotFoundError: No module named ‘jupyter_server.contents</font>   

安装完成后，无法启动，出现上述错误，是由于traitlets版本造成的。
需要卸载并制定对应的版本
```
pip uninstall traitlets
pip install traitlets==5.9.0
```












### 引用  
[datacube-core](https://github.com/opendatacube/datacube-core)  
[Python虚拟环境查看指南](https://www.python100.com/html/81988.html)      
[Postgresql download](https://www.postgresql.org/download/linux/ubuntu/)  
[pgAdmin 4 (APT) download](https://www.pgadmin.org/download/pgadmin-4-apt/)  
[Jupyter notebook ](https://jupyter.org/install)  
[Python 3.12 can't build wheels for aiohttp #7739](https://github.com/aio-libs/aiohttp/issues/7739) 
[在anaconda启动jupyter notebook时出现ModuleNotFoundError: No module named ‘jupyter_server.contents‘报错](https://blog.csdn.net/xiaobin_23134/article/details/133499644)



[Python获取指定文件夹下的文件名的方法](https://www.jb51.net/article/134543.htm?timer=tc)  
