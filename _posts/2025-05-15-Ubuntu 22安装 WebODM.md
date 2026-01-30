---
layout:     post
title:      Ubuntu 22 安装WebODM
subtitle:   
date:       2025-06-03
author:     WT
header-img: img/DJI_0294.JPG
catalog: true
tags:
    - WebDOM
    - OpenDroneMap
    - ubuntu          
---  
## 背景

OpenDroneMap是一个开源的无人机拼接软件，ODM是命令行版本，WebODM是基于Web的界面版本，前者免费，后者可以自己手动安装，也可以购买官方的打包安装版本。
本文选择在Ubuntu 22.04版本安装WebODM。

## 1 Ubuntu上安装WebODM
在 [官网](https://docs.opendronemap.org/installation/#linux)有安装说明，按照安装说明小修即可实现WebODM的安装。  
WebODM需要安装Docker、Git、Python (2 or 3)和Pip。
```
sudo apt update
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo apt install -y git python python-pip
```
可以使用 docker命令看看是否安装成功  
```
#如果带 --version的命令不行，使用第二行的命令
docker-compose --version

docker compose version

```
使用GIT下载WebDOM
```
git clone https://github.com/OpenDroneMap/WebODM
cd WebODM
sudo ./webodm.sh start
```
如果上述命令拉取Docker中出现错误：<font size=3 color=Red>Error response from daemon: Get “https://registry-1.docker.io/v2/”: net/http: request canceled (Client.Timeout exceeded while awaiting headers)</font>说明无法正常拉取。使用如下命令需要修改Docker源。
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
    "registry-mirrors": [
        "https://docker.xuanyuan.me"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

再次运动启动命令，运行成功后在浏览器中输入： http://localhost:8000


关闭webdom
```
./webodm.sh stop
```
<font size=3 color=Red>CPU with more cores will allow for faster processing, while a graphics card (GPU) currently has no impact on performance.</font>

虽然WebODM不能用GPU，但我们还是要知道Ubuntu下如何查看GPU的使用。
直接在终端输入nvidia-smi可以得到显卡情况：
```
nvidia-smi
```
如果希望自动刷新这个命令，可以输入如下命令：
```
nvidia-smi -l
```
如上方式会显示历史信息和当前信息，如果只想看当前信息，则可以执行如下命令实现每1s刷新一次：
```
watch -n 1 nvidia-smi
```
还可以有其他工具，详见引用文献的最后一篇。





## 引用文献
[Install on Ubuntu / Debian](https://docs.opendronemap.org/installation/#linux)  
[Installation and Getting Started](https://github.com/OpenDroneMap/docs/blob/publish/source/installation.rst)  
[Docker Compose 安装](https://zhuanlan.zhihu.com/p/27482200547)    
[2025 Docker/DockerHub 国内镜像源/加速列表（6月1日更新-长期维护）](https://zhuanlan.zhihu.com/p/24461370776)  
[【工具篇】如何优雅地监控显卡(GPU)使用情况？](https://zhuanlan.zhihu.com/p/577533593)  
[WebODM国内镜像](https://gitcode.com/gh_mirrors/we/WebODM/?utm_source=artical_gitcode&index=bottom&type=card&&login=from_csdn&isLogin=1)






