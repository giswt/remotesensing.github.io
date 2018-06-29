---
title: 基于Java的PostGIS开发
tags: [Java,PostGIS,Postgresql]
categories: [DB] 
icon: fa-database
layout:     post
title:      基于Java的PostGIS开发
subtitle:   
date:       2018-6-29
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - PostGIS
	- Java
	- Postgresql
---


1、 版本号
Postgresql: 9.6.6
PostGIS:2.4
jdbc: postgresql-42.2.0.jar、postgis-jdbc-2.1.7.jar

2、问题
    [PostGIS官方文档中的JDBC例子](http://www.postgis.net/docs/manual-2.4/ch06.html#idm2964)
     ((org.postgresql.PGConnection)conn).addDataType("geometry",Class.forName("org.postgis.PGgeometry"));
    ((org.postgresql.PGConnection)conn).addDataType("box3d",Class.forName("org.postgis.PGbox3d")); 错误，提示无匹配的函数
3、解决方案
   强制类型转换
   ((org.postgresql.PGConnection)conn).addDataType("geometry", (Class<? extends PGobject>) Class.forName("org.postgis.PGgeometry"));
   ((org.postgresql.PGConnection)conn).addDataType("box3d", (Class<? extends PGobject>) Class.forName("org.postgis.PGbox3d"));

