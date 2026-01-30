---
layout:     post
title:      Java调用Kettle时，报Can't run transformation due to plugin missing错误，但使用spoon执行没有问题
subtitle:   
date:       2018-6-29
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Kettle 
---
### 问题 ###
	Java调用Kettle时，报Can't run transformation due to plugin missing错误，但使用spoon执行没有问题
### 解决方案 ###

  原因：需要增加kettle的插件 地址  
  StepPluginType.getInstance().getPluginFolders().add(new PluginFolder("C:\\Program Files\\pdi-ce-7.1.0.0-12\\data-integration\\plugins", false, true));     
  KettleEnvironment.init();
 
 引自[stackoverflow](https://stackoverflow.com/questions/44866152/pentaho-kettle-cant-run-transformation-due-to-plugin-missing/44870339#44870339?newreg=26f3c19e438446d880f2fa0590efd7be)