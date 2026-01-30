---
layout:     post
title:      Ubuntu（Mint）+nginx+gunicorn部署Flask
subtitle:   
date:       2019-01-18
author:     WT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Flask
    - Ubuntu
    
---


## 1.创建虚拟环境
```
pip3 install virtualenv 
mkdir wwwroot
mkdir flask
virtualenv --no-site-packages venvflask
source venvflask/bin/activate
```
命令virtualenv创建一个独立的Python运行环境，参数--no-site-packages使得已安装到系统Python环境中第三方包不会复制，生成一个不带任何第三方包的“干净”的Python运行环境。
激活环境使用 source venvflask/bin/activate，停止该环境 使用 deactivate。

## 2.安装gunicorn和flask

在虚拟环境下分别执行
```
pip3 install gunicorn
pip3 install flask
```

将下列文件保存到helloworld.py
```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
  return 'Hello World!'

if __name__ == '__main__':
  app.run()
```

同时将helloworld.py，上传到/home/wt/wwwroot/flask，尝试执行
```
python helloworld.py
```
在本机浏览器访问 http://127.0.0.1:5000，如果显示Hello World!则说明环境没有问题
启动gunicorn 其中：-w 3表示开3个线程，-b 120.0.0.1:8000表示路径设置，helloworld:app：helloworld表示入口文件，app表示主函数
```
gunicorn -w 3 -b 127.0.0.1:8000 helloworld:app
```

## 3.安装nginx并配置
 sudo apt-get install nginx
 配置文件  /etc/nginx/sites-available/default.conf(Ubuntu mint)部分修改如下：
 ```
 server

{

listen 80;

server_name 192.168.3.7; # 这是HOST机器的外部域名，用地址也行

location / {

proxy_pass http://127.0.0.1:8000; # 这里是指向 gunicorn host 的服务地址

proxy_set_header Host $host;

proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

}

}
```
保存后重新启动 nginx
```
sudo service nginx restart
```



## 4.将 gunicorn 作为系统服务
在 /etc/systemd/system 目录下创建 flask.servie,写入以下内容：

```
[Unit]
Description=Gunicorn instance to serve the flask application

After=network.target

[Service]

User=wt

Group=www-data

Environment="PATH=/home/wt/wwwroot/flask/venvflask/bin"

WorkingDirectory=/home/wt/wwwroot/flask

ExecStart=//home/wt/wwwroot/flask/venvflask/bin/gunicorn -w 3 -b 127.0.0.1:8000 helloworld:app

ExecReload=/bin/kill -s HUP $MAINPID

ExecStop=/bin/kill -s TERM $MAINPID

[Install]

WantedBy=multi-user.target
```
启动服务
```
sudo systemctl start flask //启动
```
在浏览器的地址栏输入 http://192.168.3.7 可以看到 helloworld

更新helloworld.py后，重新上传替代文件，重启再运行gunicorn

```
sudo systemctl stop flask //启动

sudo systemctl start flask //停止
```










引用文献：[Nasser](https://www.jianshu.com/p/1c4d4f3ee52a)  
          [knarfeh](https://www.cnblogs.com/knarfeh/p/5616515.html) 
		  [Justin Ellingwood](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-gunicorn-and-nginx-on-ubuntu-16-04) 
	  
  
  
  
