---
layout:     post
title:      Oracle SDE数据维护
subtitle:   
date:       2020-08-31
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Oracle
    - SDE     
---

### 问题
Oracle 11g 的数据库 经常在密码180天过期后报ORA-12560 的错误，导致后台数据库无法连接 。 
ArcGIS Server 的授权到期后，其承载的服务会显示不全，也可能无法正常运行。

### 思路
对于密码过期导致的12560错误，以前我的处理是搜索百度，查找解决方案。
但逆向想一想，密码过期的话，是不是可以尝试修改系统时间，将系统时间修改至密码过期以前，然后重启Oracle服务或者系统即可解决。
此外，在使用Oracle自带的SQLPLUS连接时建议采用 服务器端的而非客户端的，客户端的net manager 只有数据库连接，没有监听程序。
修改后不行的话，就重启计算机。有时候未知原因更新慢。
可以参考[文献1](https://www.lxlinux.net/8540.html)将Oracle数据库的密码设置为永不过期。参考[文献2](https://pythonjishu.com/jtkkfsvrwbinndn/)查看监听器的状态，状态为 unknown，也可能时正常的。
查看状态    
`
lsnrctl status      
`  
启动监听  
`
lsnrctl start  
`


ArcGIS Server 授权到期后，需要新的授权文件，然后在开始菜单-->ArcGIS Server 下有 Software Authorization可执行程序，更新授权文件，然后查看 C盘 program files ESRI 下的keycode文件是否更新，再通过网页 manager 查看授权日期有没有更新，如果没有更新，建议重启系统或者重启ArcGIS Server服务。
Server授权更新后，ArcMap通过ArcSDE连接Oracle时仍然后出现需要授权的情况，此时如果密码过期的问题没有解决，即使选择了正确的授权文件，仍然无法完成授权更新，必须前一步过期的问题解决了再授权。

言简意赅的操作：将日期改回失效前，更新ArcGIS Server授权和Oracle授权，修改Oracle密码过期期限，改回正确的日期，重启计算机。


引用文献：  
[Oracle中设置账户密码永不过期具体方法](https://www.lxlinux.net/8540.html)   
[Oracle数据库ORA-12560错误问题的解决办法](https://pythonjishu.com/jtkkfsvrwbinndn/)  


