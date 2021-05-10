---
title: "使用nginx-+-uWSGI运行flask程序"
categories: [ "服务器技术" ]
tags: [ "nginx","uswgi","flask","virtualenv" ]
draft: false
slug: "nginx_uwsgi_flask"
date: "2015-04-27 11:46:00"
url: "nginx_uwsgi_flask.html"
---

老高最近写了一个[flask小应用][1]，功能很简单，就是通过读取配置再显示ss的二维码配置，不过想让他运行起来统一管理，还是费了一些周折啊。

如果你自己Google过，你可能会发现一个关键字uWSGI，他就是本篇的主角。

名词解释

Web服务器网关接口（Python Web Server Gateway Interface，缩写为WSGI。
uwsgi与WSGI一样，是一个通信协议。
uWSGI，一个实现了WSGI，uwsgi，http等协议的服务器，与nginx在一个层次。

鉴于国内的一些教程都写成翔了，那老高就赶紧记录分享一下。


<!--more-->


> ps. 这个教程不限定于flask、web.py、django同样适用。

## 安装环境

Centos x64
python 2.6


## 安装uWSGI

```
pip install uWSGI
```

## 安装配置virtualenv

一般我们会用virtualenv配置项目的运行环境

```
pip install virtualenv

# 假设 ~/app_dir 为你的项目目录
mkdir ~/app_dir && cd ~/app_dir

# 最简单的flask示例
vi hello_world.py

# add

from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run('0.0.0.0', 8080)

--------:wq

virtualenv venv

# 激活virtualenv
. venv/bin/activate

#pip install flask qrcode image
pip install flask

# 退出virtualenv
deactivate
```

## 测试运行

项目与运行环境已经准备完毕，我们先用uWSGI现在试试运行项目。

```
# 激活virtualenv
. venv/bin/activate
python hello_world.py

# 现在访问你的ip:8080试试看
```

## nginx配置

这里我们使用http反向代理

```
vi hello_world.conf

server{
    listen 80;
    location / {
        proxy_pass        http://127.0.0.1:9090;
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP  $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   Referer http://$host;
    }
}
```

## 运行uWSGI服务器

```
uwsgi --http-socket 127.0.0.1:9090 --wsgi-file /root/app_dir/hello_world.py --callable app -H /root/app_dir/venv
或
uwsgi --http :9090 --wsgi-file /root/app_dir/hello_world.py --callable app -H /root/app_dir/venv
```

以上命令我们使用http运行uWSGI服务器，绑定端口9090，用绝对路径指定了入口文件hello_world.py，并使用-H参数指定virtualenv的地址。

当然，运行uWSGI服务器的方式还有很多种，比如我们可以以配置文件的形式启动，再比如我们还可以使用sock的形式运行，当然相应的nginx反响代理也需要同时修改。



Reference

http://stackoverflow.com/questions/7739810/wsgi-vs-uwsgi-with-nginx
http://uwsgi-docs.readthedocs.org/en/latest/Protocol.html


  [1]: https://github.com/phpgao/qrcode-for-ss