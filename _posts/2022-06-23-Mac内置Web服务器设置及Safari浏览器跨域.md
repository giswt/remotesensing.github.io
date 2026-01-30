---
layout:     post
title:      Mac内置Web服务器设置及Safari浏览器跨域
subtitle:   
date:       2022-06-23
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Mac
    - Web服务器  
    - 跨域 
---

### Mac内置Web服务器设置  
Mac OS 系统自带了Apache服务器，启动和关闭的方法如下：

```
$ sudo apachectl start       // 启动。 此默认情况下对应电脑路径为“LibraryWebServer/Document/” 
$ sudo apachectl stop     // 关闭。 
$ sudo apachectl restart     // 重新启动 
```

### Safari浏览器跨域问题

safari浏览器 -> 偏好设置 -> 高级 -> 勾选最底部的开发菜单
在safari浏览器顶部“开发”菜单项中，勾选停用跨源限制

引用文献：  
         [Mac OS X 启用 Web 服务器](https://www.jianshu.com/p/d006a34a343f)
         [Mac 自带 Apache 多端口配置](https://www.jianshu.com/p/45731dcc395e)
         [Safari 解决跨域问题：Access-Control-Allow-Origin](https://www.jianshu.com/p/dbf08919223c)
         	 
