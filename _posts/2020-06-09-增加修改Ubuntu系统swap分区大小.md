---
layout:     post
title:      增加修改Ubuntu系统swap分区大小
subtitle:   
date:       2020-06-09
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Ubuntu
    
---


### 前因 ###

在Ubuntu运行影像处理程序时会出现内存被家族的情况，无法增加物理内存的情况下只能增加swap分区的大小了。


  
### 步骤 ###

1. 进入你要创建的swap文件的路径下

2. sudo dd if=/dev/zero of=/home/swapfile bs=1M count=3k #(其中of=是指创建的文件名称，创建3G(bs*count)的swap,)

3. sudo mkswap swapfile #将刚才新建的文件变成swap分区

4. sudo swapon swapfile # 设置swapfile分区有效，即开关的作用。失效为swapoff命令

输入输入free命令，看到swap分区大小

如果想下次开机，新增加的swap分区依然生效，则需要修改/etc/fstab文件，让系统记住你增加的文件、文件类型、大小等信息

在/etc/fstab文件最后增加：/home/xxx/swapfile swap swap defaults 0 0

引用文献：[lengxibo](https://blog.csdn.net/lengxibo/article/details/8303113)  