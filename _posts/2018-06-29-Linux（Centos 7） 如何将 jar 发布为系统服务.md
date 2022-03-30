---
layout:     post
title:      Linux（Centos 7） 如何将 jar 发布为系统服务
subtitle:   
date:       2018-6-29
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true

tags:
    - Linux
    - jar  
    - service 
---

1、在/etc/systemd/system路径下创建文件 javaservice.service
内容如下：
```
[Unit]
Description=Java Service

[Service]
User=nobody
# The configuration file application.properties should be here:
WorkingDirectory=/data 
ExecStart=/usr/bin/java -Xmx256m -jar application.jar
SuccessExitStatus=143
TimeoutStopSec=10
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

2、重新加载 使得系统识别该服务
```
systemctl daemon-reload
```
3、启用服务，使它能够随系统启动
```
systemctl enable javaservice.service
```
4、启用后 就可以使用系统命令操作该服务了
```
systemctl start javaservice
systemctl stop javaservice
systemctl restart javaservice
systemctl status javaservice
```

  引自[stackoverflow,yglodt](https://stackoverflow.com/questions/21503883/spring-boot-application-as-a-service/22121547#)

  
  
  
