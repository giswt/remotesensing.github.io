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
``` python
class C:
    explanation='this is my programs'
    #普通方法或实例方法
    def normalMethod(self,explanation):
        self.explanation=explanation
        print(self.explanation)
        
    def normalMethod2(self,explanation):
        #self.explanation=explanation
        print(self.explanation)
    #类方法，可以访问属性
    @classmethod
    def classMethod(cls,explanation):
        print(cls.explanation)
    @classmethod
    def classMethod2(cls,explanation):
        cls.explanation=explanation
        print(cls.explanation)       
        
    #静态方法，不可以访问类属性
    @staticmethod
    def staticMethid(explanation):
        print(explanation)

c=C()
c.explanation="this is new"
print("result 0:")
c.normalMethod2('explanation')
print("result 1:")
c.normalMethod('explanation')
print("result 2:")
c.classMethod('explanation')
print("result 3:")
C.classMethod('explanation')
print("result 4:")
c.staticMethid('explanation')
print("result 5:")
C.staticMethid('explanation')

print("result 6:")
C.classMethod2('explanation1')
print("result 7:")
c.classMethod2('explanation2')
print("result 8:")
c.normalMethod2('explanation3')

print("result 9:")
C.normalMethod2('explanation3')
```

<center><img src="http://www.spatial.pro/img/I20191024python2.png"   /></center>
result 0 表明实例c的explanation属性被成功赋值

result 1 表明 普通方法 normalMethod 能改变实例c的explanation属性

result 2 表明 类C的explanation属性没有因为实例c对实例变量explanation的改变而改变

result 3 同上

result 4 result 5 表明 类C和实例c都可以调用静态方法（静态方法不能调用类）

result 6 表明 类C的变量可以通过类C调用类方法修改

result 7 表明 类C的变量可以通过实例c调用类方法修改

result 8 表明 实例c的explanation值不会因为cls的explanation改变而改变，是不同的变量

result 9 表明 实例方法不能被类直接调用





引用文献：[SmallTankPy的博客](https://blog.csdn.net/SmallTankPy/article/details/54945135)  
          [jayhuang的博客](https://www.cnblogs.com/jayliu/p/9030725.html)  
		
	  
  
  
  
