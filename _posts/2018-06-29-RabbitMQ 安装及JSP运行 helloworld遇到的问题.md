---
icon: fa-bug 
layout:     post
title:      RabbitMQ 安装及JSP运行 helloworld遇到的问题
subtitle:   
date:       2018-6-29
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - RabbitMQ
	- JSP 
  
---
### RabbitMQ ###  

RabbitMQ   Server 安装环境 windows8(64位),安装的版本 3.7.2。
它依赖于Erlang，我下载的版本是20.1（otp_win64_20.1.exe），官网下载很慢。
安装很顺利，直接下一步即可。
开发语言使用的Java，所以下载了对应的Client API,由于5.x版本使用的是JDK8，我的计算机上安装的是JDK7，因此下载了4.x(amqp-client-4.4.1.jar)。

### JSP ###
JSP导入多个类库
	<%@ page import="com.rabbitmq.client.ConnectionFactory,com.rabbitmq.client.Connection,com.rabbitmq.client.Channel"%>
JSP报错
    Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/LoggerFactory
	解决方法：引入slf4j-api.jar和slf4j-log4j12.jar，我使用了slf4j-1.7.9.zip，里面有很多包，只使用这两个，否则会冲突
	Caused by: java.lang.NoClassDefFoundError: org.apache.log4j.Level
	解决方法：引入log4j.jar 我使用了log4j-1.2.15.jar.zip
	org.apache.jasper.JasperException: java.lang.ClassNotFoundException: org.apache.jsp
	解决方法：可能是包冲突造成的，引入重复或不同版本的包或者类