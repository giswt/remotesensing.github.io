---
layout:     post
title:      Linux下安装scikit-learn
subtitle:   
date:       2018-7-25
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Linux
	- 机器学习
	- Python
    - scikit-learn

---
### 方法一：###
	1、安装python-dev，安装这个包，以后安装各种python扩展包，可以省很多事情。
	
		sudo apt-get install python-dev

    2、使用apt-get 安装以下包

		sudo apt-get install python-numpy
		sudo apt-get install python-scipy
		sudo apt-get install python-matplotlib
		
	3、安装scikit-learn
	
		sudo pip install -U scikit-learn
		
### 方法二：###
	ubuntu14.04安装scikit-learn（Python 2.7） 
	1、安装依赖 
	
		sudo apt-get install build-essential python-dev python-numpy python-setuptools python-scipy libatlas-dev libatlas3-base 
	
	2、配置matplotlib库，进行画图之类的 
	
		sudo apt-get install python-matplotlib 
		
	3、配置scikit-learn库 
	
		sudo apt-get install python-sklearn 
	
### 验证安装是否成功：###

	python 
	import numpy 
	import scipy 
	import matplotlib 
	import sklearn 
	
	不报错，安装成功
	
摘自：[huifu1988的博客](https://blog.csdn.net/shuifu1988/article/details/77429234)  
	  [Shomy的专栏](https://blog.csdn.net/shomy_liu/article/details/48543449)

