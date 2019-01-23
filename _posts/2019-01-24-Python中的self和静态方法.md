---
layout:     post
title:      Python中的self，实例方法、类方法及静态方法
subtitle:   
date:       2019-01-18
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Python

    
---


## 1.self
``` python
class A():
    def go(self):
        self.one='hello'
class B():
    def go(self):
        one='hello'
a1=A()
a1.go()
print(a1.one)
b1=B()
b1.go
print(b1.one)
```
<center><img src="http://www.spatial.pro/img/I20191024python1.png"   /></center>
带有self的变量为类的变量，可以被实例引用，不带self的变量为函数的局部变量，作用域仅限于函数本身，不能被实例引用


## 2.实例方法、类方法及静态方法




引用文献：[SmallTankPy的博客](https://blog.csdn.net/SmallTankPy/article/details/54945135)  
          
		
	  
  
  
  
