---
layout:     post
title:      postgres无法正常连接数据库no pg_hba.conf entry for host
subtitle:   
date:       2018-6-29
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Postgresql
---
当程序链接PostgreSQL 时，可能会报错：No pg_hba.conf entry。

这条错误的原因是因为客户端远程访问postgresql受限所致，因为postgresql默认情况下除本机外的机器是不能连接的。

默认情况下，postgresql本机的连接是信任连接,只要有用户名就可以连接,不用密码. 要改变这种默认设置需要更改postgresql安装目录下的两个配置文件(PostgreSQL数据库的安装目录下/data/pg_hba.conf与postgresql.conf）

### 认证文件 pg_hba.conf###  

pg_hba.conf每条记录声明一种联接类型，一个客户端 IP 地址范围（如果和联接类型相关的话），一个数据库名，一个用户名字，以及对匹配这些参数的联接使用的认证方法。

例如：

[python] view plain copy
##Added by liu.chunming  
local  all             all                                          peer  
host   all             all           127.0.0.1/32                   md5  
host   all             all           ::1/128                        md5  
联接使用的认证方法：
trust
        无条件地允许联接。这个方法允许任何可以与PostgreSQL数据库服务器联接的用户以他们期望的任意PostgreSQL 数据库用户身份进行联接，而不需要口令。

md5
        要求客户端提供一个 MD5 加密的口令进行认证。

如果我想让10.86.12.0~10.86.12.154的IP段能访问PostgreSQL 数据库，需要增加下面一行：

[python] view plain copy
host   all             all           10.86.12.0/24                  trust  

下面说下地址后面的那个值，是如何确定的

A类的默认子网掩码　255.0.0.0　        一个子网最多可以容纳1677万多台电脑
B类的默认子网掩码　255.255.0.0　   一个子网最多可以容纳6万台电脑
C类的默认子网掩码　255.255.255.0　一个子网最多可以容纳254台电脑
 
把子网掩码切换至二进制，我们会发现，所有的子网掩码是由一串连续的1和一串连续的0组成的（一共4段，每段8位，一共32位数）。
255.0.0.0　11111111.00000000.00000000.00000000            看下 你电脑的子网掩码配置 是这个就写8
255.255.0.0　11111111.11111111.00000000.00000000        看下 你电脑的子网掩码配置 是这个就写16
255.255.255.0　11111111.11111111.11111111.00000000     看下 你电脑的子网掩码配置 是这个就写24
 
例如：
我的电脑是255.255.0.0   ip地址192.168.0.69，我就写成192.168.0.69/16即可
### 配置文件 postgresql.conf###  

要让本机外的机器访问,除了在pg_hba.conf中增加访问的IP段外，这设置外还要把postgresql.conf里的监听地址设置为:listen_addresses = '*'

摘自：[luweifeng1983](http://blog.csdn.net/luweifeng1983/article/details/3980141)  
      [chinadyw](https://www.cnblogs.com/chinadyw/p/3507207.html)  
	  [刘春明的博客](http://blog.csdn.net/liuchunming033/article/details/44810899)  



  
  
  
