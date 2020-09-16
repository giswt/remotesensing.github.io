---
layout:     post
title:      GeoServer跨域
subtitle:   
date:       2020-09-04
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - GeoServer  
---
<font color="#dd0000">注意：本文GeoServer版本 2.17.2</font>  
GeoServer 使用了Jetty作为Web服务器发布数据服务，本文主要讲述GeoServer版本 2.17.2版本的跨域设置。     
步骤  

###  1、修改文件web.xml  
web.xml在webapps/geoserver/WEB-INF目录下，将涉及跨域的两部分注释去掉。    
第一部分：  
```
<!-- Uncomment following filter to enable CORS -->
<filter>
  <filter-name>cross-origin</filter-name>
     <filter-class>org.eclipse.jetty.servlets.CrossOriginFilter</filter-class>
  </filter>
  ......
```
第二部分
```
<!-- Uncomment following filter to enable CORS -->
<filter-mapping>
   <filter-name>cross-origin</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```
### 2、重新启动Geoserver

### 引用  
[Enabling CORS in GeoServer (jetty)?](https://gis.stackexchange.com/questions/210109/enabling-cors-in-geoserver-jetty)  
