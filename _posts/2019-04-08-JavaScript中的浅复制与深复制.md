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
1、数值型
```
var x = 1;
var y = x; //y获得了和x同样的值
y = 2;
console.log(x); // 1
```
2、字符型
```
var str1 = "hello world ! ";
var str2 = str1;
str2 = "hello zxy !";
alert("str1 = "+str1); //hello world ! 
alert("str2 = "+str2);//hello zxy !
```
3、布尔型
```
var bo1 = true;
var bo2 = bo1;
bo2 = false;
alert("bo1 = "+bo1); //bo1 = true
alert("bo2 = "+bo2);//bo2 = false
```
基础类型的拷贝可以使用等号（=），并且修改任意一个，不会影响到另外的值.

## 复杂类型的复制

1、对象类型

```
var person1 = {
        name:"zhangyu",
        age : 8
    };

    var person2 = person1;
    person2.name = "zhanglong";
    person2.age = 10;

    alert("person1.name = "+person1.name +"...person1.age = "+person1.age);
	//person1.name = zhanglong...person1.age = 10
    alert("person2.name = "+person2.name +"...person2.age = "+person2.age);
	//person1.name = zhanglong...person1.age = 10
	
```


而对于对象的深拷贝，没有内置方法可以使用，我们可以自己命名一个函数进行这一操作：
	
```
	var objDeepCopy = function(source){
		var sourceCopy = {};
		for (var item in source) sourceCopy[item] = source[item];
		return sourceCopy;
	}
	
```

但是对于复杂结构的对象我们发现这个函数并不适用，例如：

var obj = { "a": { "a1": ["a11", "a12"], "a2": 1 }, "b": 2 };
所以需要进行一点修改：

```
	var objDeepCopy = function(source){
		var sourceCopy = {};
		for (var item in source) sourceCopy[item] = typeof source[item] === 'object' ? objDeepCopy(source[item]) : source[item];
		return sourceCopy;
	}
	var objCopy = objDeepCopy(obj);
	objCopy.a.a1[1] = "a13";
	obj   // => { "a": { "a1": ["a11", "a12"], "a2": 1 }, "b": 2 }
	objCopy   // => { "a": { "a1": ["a11", "a13"], "a2": 1 }, "b": 2 }
	
```
	
	
2、数组

```
var arr1 = [1,2,3,4,5,6];
var arr2 = arr1;
arr2[0] = 88;
alert("arr1[0] = "+arr1[0]);//arr1[0] = 88
alert("arr2[0] = "+arr2[0]);//arr2[0] = 88

```


对于数组的深拷贝常规的有三种方法：

方法一：遍历复制

```

	var arr = ["a", "b"], arrCopy = [];
	for (var item in arr) arrCopy[item] = arr[item];
	arrCopy[1] = "c";
	arr   // => ["a", "b"]
	arrCopy   // => ["a", "c"]
	
```
	
考虑伪多维数组可以写成函数形式：

```

	function arrDeepCopy(source){
		var sourceCopy = [];
		for (var item in source) sourceCopy[item] = typeof source[item] === 'object' ? arrDeepCopy(source[item]) : source[item];
		return sourceCopy;
	}
	
```

这种方法简单粗暴，但是利用JS本身的函数我们可以更加便捷地实现这个操作。

方法二：slice()

可以参考 W3School 对 slice() 方法的描述：slice() 方法可从已有的数组中返回选定的元素。
调用格式为：
arrayObject.slice(start,end)
方法返回一个新的数组，包含从 start 到 end （不包括该元素）的 arrayObject 中的元素。该方法并不会修改数组，而是返回一个子数组。
在这里我们的思路是直接从数组开头截到尾：
	
```
	arrCopy = arr.slice(0);
	arrCopy[1] = "c";
	arr   // => ["a", "b"] 
	arrCopy   // => ["a", "c"]
	
```

可以看出成功创建了一份原数组的拷贝。

方法三：concat()

可以参考 W3School 对 concat() 方法的描述：concat() 方法用于连接两个或多个数组。
调用格式为：
arrayObject.concat(arrayX,arrayX,......,arrayX)
该方法不会改变现有的数组，而仅仅会返回被连接数组的一个副本。
使用这种方法的思路是我们用原数组去拼接一个空内容，放回的便是这个数组的拷贝：
	
```
	arrCopy = arr.concat();
	arrCopy[1] = "c";
	arr   // => ["a", "b"] 
	arrCopy   // => ["a", "c"]
	
```

数组、对象、对象数组进行简单赋值运算只是创建了一份原内容的引用，指向的仍然是同一块内存区域，修改时会对应修改原内容

对象数组的深拷贝

如果再考虑更奇葩更复杂的情况，例如我们定义：

var obj = [{ "a": { "a1": ["a11", "a12"], "a2": 1 }, "b": 2 }, ["c", { "d": 4, "e": 5 }]];
这是一个由对象、数组杂合成的奇葩数组，虽然我们平时写程序基本不可能这么折腾自己，但是可以作为一种特殊情况来考虑，这样我们就可以结合之前说的方法去拓展拷贝函数：

	```
	var objDeepCopy = function (source) {
		var sourceCopy = source instanceof Array ? [] : {};
		for (var item in source) {
			sourceCopy[item] = typeof source[item] === 'object' ? objDeepCopy(source[item]) : source[item];
		}
		return sourceCopy;
	}
	var objCopy = objDeepCopy(obj);
	objCopy[0].a.a1[1] = "a13";
	objCopy[1][1].e = "6";
	obj   // => [{ "a": { "a1": ["a11", "a12"], "a2": 1 }, "b": 2 }, ["c", { "d": 4, "e": 5 }]]
	objCopy   // => [{ "a": { "a1": ["a11", "a13"], "a2": 1 }, "b": 2 }, ["c", { "d": 4, "e": 6 }]]
	```
	
这样它就可以作为一个通用函数替我们进行深拷贝操作了

需要注意下面的题，需要对JS数组有更深的了解

```
	var a = [1,2,3];
	var b = a;
	a = [4,5,6];
	alert(b);  //[1,2,3]
	
	var a = [1,2,3];
	var b = a;
	a.pop();
	alert(b);  //[1,2]
```

![javascriptcopy](http://www.spatial.pro/img/javascriptcopy.jpg)	



3、函数类型

```
var func1 = function () {
        alert("我是func1函数1！");
    }

    var func2  = func1;

    func2 = function () {
        alert("我是修改后的func2函数！");
    }

    func1();//我是func1函数1！
    func2();//我是修改后的func2函数！
```


  


引用文献：[xinghuo0007的博客](https://blog.csdn.net/xinghuo0007/article/details/74065184)    
		  [南不过远洋的博客](https://www.cnblogs.com/jiangzilong/p/6513552.html)
		  [知乎](https://www.zhihu.com/question/26042362)
          
		
	  
  
  
  
