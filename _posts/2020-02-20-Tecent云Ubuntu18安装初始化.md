---
layout:     post
title:      Tecent云Ubuntu18安装初始化
subtitle:   
date:       2020-02-20
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Tencent
    - Ubuntu
    
---


### 安装桌面 ###

1. sudo apt-get install xinit

2. sudo apt-get install lightdm  可以选择gdm3 但我认为不好用

3. sudo apt-get install ubuntu-desktop
  
### 安装sudo xrdp ###

1.sudo apt install xrdp
2.sudo systemctl enable xrdp

### windows远程桌面连接 ###

1.windows打开远程桌面输入ubuntu主机ip或者主机名
2.选择xorg，输入用户名密码
3.会提示几次授权修改主机的颜色设置什么的，都可以cancel掉，然后即可登陆成功


引用文献：[无名小卒917的360doc](http://www.360doc.com/content/14/1110/15/18144428_424066808.shtml)  
	  [百度经验](https://jingyan.baidu.com/article/148a19218d77b64d71c3b1b3.html)
	  
	  
  
  
  
