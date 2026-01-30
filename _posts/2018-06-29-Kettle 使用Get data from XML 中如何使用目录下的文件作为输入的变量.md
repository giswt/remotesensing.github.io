---
layout:     post
title:      Kettle 使用Get data from XML 中如何使用目录下的文件作为输入的变量
subtitle:   
date:       2018-6-29
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Kettle 
---

### 解决方案 ###
You should give the directory structure in the file/direcory and in the regex give .\*txt instead of \*.txt then add and use Get Filenames to see whther you are getting all the files .
这个不符合逻辑，但目前只能这样这样，正则表达式 .\*xml 代表目录下所有的xml文件
What you enter as spec is a regular expression .\*txt ... means any file ending on txt (. is a wildcard character, * means 0 or more times).
  
 
 引自[Pentaho Community Forums ,Sreelatha
](https://forums.pentaho.com/archive/index.php/t-56428.html)