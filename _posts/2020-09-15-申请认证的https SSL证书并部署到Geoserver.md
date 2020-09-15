---
layout:     post
title:      申请认证的https SSL证书并部署到Geoserver
subtitle:   
date:       2020-09-15
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - https
    - SSL  
    - GeoServer  
---
本文是[GeoServer启用https](http://www.spatial.pro/2020/09/04/GeoServer%E5%90%AF%E7%94%A8https/)的补充。  
步骤：  
### 1、SSL证书的获取
免费的SSL证书可以在[SSL For Free](https://www.sslforfree.com/)网站上申请，具体步骤可以百度或Google ，也可以参考[文献1](https://free.com.tw/ssl-for-free/)。  
下载的压缩包包含三个文件private.key、certificate.和ca_bundle.crt。

#### 合并转换SSL证书
首先，使用OPENSSL根据上面的三个文件生成PKCS12证书
```
openssl pkcs12 -export -out j2vproject.pkcs12 -inkey private.key -in certificate.crt -certfile ca_bundle.crt
``` 
然后，利用Keytool创建keystore文件。
```
keytool -v -importkeystore -srckeystore j2vproject.pkcs12 -srcstoretype PKCS12  -destkeystore keystore -deststoretype JKS
```
生成的keystore会提示格式旧，可以按照提示说明运行命令。
```
keytool -importkeystore -srckeystore keystore -destkeystore keystore -deststoretype pkcs12
```
### 替换keystore文件
将生成的keystore文件替换Geoserver安装目录下的/usr/share/geoserver/etc和/usr/share/geoserver/modules/ssl目录下的keystore文件。
在maven目录https://repo1.maven.org/maven2/org/eclipse/jetty/jetty-distribution/ 下找到对本的版本（本文是9.4.18.v20190429），下载zip版本，解压备用。
#### 重启Geoserver
```
$ sudo systemctl stop geoserver
$ sudo systemctl start geoserver
```
使用https访问Geoserver，浏览器会出现小锁的标志
![https_ssl](http://www.spatial.pro/img/https_ssl.png)   
  
  
#### 引用  
[SSL For Free 免費 SSL 憑證申請，使用 Let’s Encrypt 最簡單方法教學！](https://free.com.tw/ssl-for-free/)  
[Create java keystore from private key and CA certificate bundle](https://stackoverflow.com/questions/30900915/create-java-keystore-from-private-key-and-ca-certificate-bundle)  

