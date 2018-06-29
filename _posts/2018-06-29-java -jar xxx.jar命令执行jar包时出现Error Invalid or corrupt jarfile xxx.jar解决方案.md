---
layout:     post
title:      java -jar xxx.jar命令执行jar包时出现Error Invalid or corrupt jarfile xxx.jar解决方案
subtitle:   
date:       2018-6-29
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Java
---



1、问题
    java -jar xxx.jar命令执行jar包时出现Error: Invalid or corrupt jarfile xxx.jar
2、解决方案
   指定META-INF/MANIFEST.MF的路径为src下（注意不要放到main/java目录下，否则打成的jar中META-INF/MANIFEST.MF不含有Main Class信息或者没有MANIFEST.MF）

