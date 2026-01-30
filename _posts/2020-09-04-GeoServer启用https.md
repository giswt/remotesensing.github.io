---
layout:     post
title:      GeoServer启用https
subtitle:   
date:       2020-09-04
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - https
    - GeoServer  
---
<font color="#dd0000">注意：本文GeoServer版本 2.17.2</font>  
GeoServer 使用了Jetty作为Web服务器发布数据服务，提供管理界面，本文主要讲述GeoServer版本 2.17.2版本的https启用方法   
步骤：  
### 1、使用JDK的keytool创建证书
keytool 所在的目录为JVM目录下 /usr/lib/jvm/java-1.8.0-openjdk-amd64/jre/bin(openjdk安装见[上一篇](http://www.spatial.pro/2020/09/03/%E5%9C%A8Ubuntu%E4%B8%8A%E5%AE%89%E8%A3%85GeoServer/))    
运行以下命令，创建keystore，执行过程中需要输入姓名、国家等信息以及keystore password（密码后续会用到）
```
keytool -keystore keystore -alias jetty -genkey -keyalg RSA
```
验证keystore的可用性(password输入上一步的密码)
```
keytool -list -keystore keystore -storepass password
```
成功后内容如下：
```
Keystore type: JKS
Keystore provider: SUN

Your keystore contains 1 entry

le-f8a123c3-abcd-4bbb-b341-40251cf90a0b, 02.jun.2018, PrivateKeyEntry,
Certificate fingerprint (SHA1): A1:BF:53:7F:30:00:11:22:33:44:8D:F4:8A:20:25:FF:6B:D5:89:7C

Warning:
The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore keystore -destkeystore keystore -deststoretype pkcs12".
```
将keystore复制到GeoServer目录 /usr/share/geoserver/etc，原先的keystore文件备份  
### 2、查看jetty版本号，补充和替换相关文件
需要补充和替换相关文件的原因是GeoServer升级的过程中，对应的启用https相关的文件并没有升级,原文见stackoverflow的[IT_User](https://stackoverflow.com/questions/61220799/jetty-https-module-causing-startup-to-fail)评论。

#### 查看jetty版本号  
在GeoServer安装目录（/usr/share/geoserver）下运行以下命令查看
```
java -jar start.jar --version
```
也可以直接到lib目录（/usr/share/geoserver/lib）直接查看查看jetty版本号 
#### 下载查看jetty完整版
在maven目录https://repo1.maven.org/maven2/org/eclipse/jetty/jetty-distribution/ 下找到对本的版本（本文是9.4.18.v20190429），下载zip版本，解压备用。
#### 替换文件
使用下载的jetty的文件替换GeoServer中的文件
替换usr/share/geoserver/etc下的jetty-http.xml、jetty-https.xml和jetty-ssl.xml文件，新增jetty-ssl-context.xml文件，在usr/share/geoserver/modules下新增ssl.mod，在modules下新建文件夹ssl，将keystore复制到该目录下。  
编辑jetty-ssl.xml文件，将KeyStorePassword、KeyManagerPassword和TrustStorePassword替换为创建keystore时输入的密码。
### 3、启用https、ssl
修改 usr/share/geoserver/start.ini文件，在# Module: http部分后增加以下内容（password为创建keystore时输入的密码）。

```
#SSL
--module=ssl
--module=https
jetty.secure.port=8443
jetty.keystore=etc/keystore
jetty.truststore=etc/keystore
jetty.keystore.password=password
jetty.keymanager.password=password
jetty.truststore.password=password
```
在usr/share/geoserver目录下运行以下命令，启用https、ssl
```
java -jar start.jar --add-to-startd=https
java -jar start.jar --add-to-startd=ssl
```
如果出现错误，说明上一步的替换文件出现问题，根据错误替换对应的文件。
### 4、启动Geoserver
```
$ cd /usr/share/geoserver/bin
$ ./startup.sh
```
如果出现java.lang.IllegalStateException: No constructor class的错误说明前面你下载的jetty-distribution的版本不对，请重新检查并下载对应的版本。  
如果没有错误，在浏览器中输入https://localhost:8443就可以通过https访问GeoServer了。


GeoServer 查看日志的方法见[Where are the GeoServer HTTP logs on Jetty server?](https://gis.stackexchange.com/questions/362767/where-are-the-geoserver-http-logs-on-jetty-server)

##### 引用  
[Access WFS services over https](https://sourceforge.net/p/geoserver/mailman/message/36700076/)  
[Jetty https module causing startup to fail](https://stackoverflow.com/questions/61220799/jetty-https-module-causing-startup-to-fail)  
[Installing a SSL certificate from a PFX keystore](http://kjetilpettersson.blogspot.com/2018/06/geoserver-2121jetty-installing-ssl.html)   
[SSL in the Jetty Distribution](https://www.eclipse.org/jetty/documentation/current/jetty-ssl-distribution.html)  
[rouble with keystore in Jetty](https://sourceforge.net/p/geoserver/mailman/geoserver-users/thread/05AABE56C244ED43A7C5796139045A53EB6B6F2D%40VMX01B.purmerend.local/#msg36014688)  
[Where are the GeoServer HTTP logs on Jetty server?](https://gis.stackexchange.com/questions/362767/where-are-the-geoserver-http-logs-on-jetty-server)  
[Geoserver and moving to HTTPS](https://stackoverflow.com/questions/60597777/geoserver-and-moving-to-https)
