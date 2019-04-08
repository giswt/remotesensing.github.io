---
layout:     post
title:      JavaScript中的浅复制与深复制
subtitle:   
date:       2019-02-26
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - JavaScript

    
---

## 简单类型的复制
数值型
```
	var x = 1;
	var y = x; //y获得了和x同样的值
	y = 2;
	console.log(x); // 1
```
字符型
```
    var str1 = "hello world ! ";
    var str2 = str1;
    str2 = "hello zxy !";
    alert("str1 = "+str1); //hello world ! 
    alert("str2 = "+str2);//hello zxy !
```
布尔型
```
```


  


引用文献：[xinghuo0007的博客](https://blog.csdn.net/xinghuo0007/article/details/74065184)     
          
		
	  
  
  
  
