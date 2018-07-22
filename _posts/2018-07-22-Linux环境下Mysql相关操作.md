---
layout:     post
title:      Linux环境下Mysql相关操作
subtitle:   
date:       2018-7-22
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Linux
    - Mysql

---
远程连接Mysql 10061错误

	可能的原因：远程端口未开放，可以使用端口探测软件探测端口是否开放，云主机要查看安全组是否开放了端口	远程登陆主机，查看是否启动了Mysql，默认端口是否进行了修改。

		1. 登录mysql
		[root@test /]# mysql -u root -p
		Enter password:
 
		2. 使用命令show global variables like 'port';查看端口号
 
		mysql> show global variables like 'port';
		+---------------+-------+
		| Variable_name | Value |
		+---------------+-------+
		| port | 3306 |
		+---------------+-------+
		1 row in set (0.00 sec)
	如果以上问题都没有，查看配置文件的bind-adress 是否修改为0.0.0.0
	配置文件的位置 网上大多说是 /etc/my.cnf   /etc/mysql/my.cnf 
	我的配置文件在 /etc/mysql/mysql.conf.d/mysqld.cnf(Version:5.7.22)
