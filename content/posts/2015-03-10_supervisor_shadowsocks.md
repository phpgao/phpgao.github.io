---
title: "使用supervisor托管shadowsocks"
categories: [ "服务器技术" ]
tags: [ "python","shadowsocks","supervisor" ]
draft: false
slug: "supervisor_shadowsocks"
date: "2015-03-10 06:14:00"
url: "supervisor_shadowsocks.html"
---

由于shadowsocks在服务器运行可能会不稳定，所以我们将shadowsocks的运行管理任务交给supervisor，这样如果ss挂了，supervisor会帮我们自动将shadowsocks重新启动，保证了ss的稳定性。

supervisor是什么我就不多介绍了，老高在此只强调一点，他是由python编写的，官网在此
http://supervisord.org/

本文也可作为supervisor的入门文章使用，欢迎大家交流！


<!--more-->


> 需要注意的是，supervisor目前还不支持PY3，所以如果强制使用PY3安装会报错

## 安装

> 安装和配置的时候需要root权限

```bash
pip install supervisor

or

easy_install supervisor
```

## 初始化

```bash
# 初始化配置文件
# 此命令会在 /etc/下创建一个示例配置文件
echo_supervisord_conf > /etc/supervisord.conf
```

## 运行配置

老高在服务器安装的shadowsocks-libev版，所以启动命令和python版的不用：

```bash
# shadowsocks-libev启动命令
ss-server -c /home/gzm/config2.json
# 原版shadowsocks启动命令
ssserver -c /home/gzm/config2.json
```

现在我们准备好了启动命令，现在开始最重要的部分。

```conf
# 在/etc/supervisord.conf文件最后添加shadowsocks实例，代码如下
# 这一段配置如果配置错误，会导致supervisor的启动失败

[program:shadowsocks]
command = ss-server -c /home/gzm/config2.json
user = phpgao
autostart = true
autoresart = true
stderr_logfile = /var/log/supervisor/ss.stderr.log
stdout_logfile = /var/log/supervisor/ss.stdout.log
```

- command就是ss的运行命令；
- user是执行命令的用户；
- autostart和autoresart指自动启动和自动重启；
- stdout_logfile重定向程序输出到此文件；
- stderr_logfile重定向程序错误到此文件；

**需要注意的是**

## 运行和更新

编辑完配置文件就可以开始运行supervisor了

### 运行supervisor

```bash
# 运行的时候使用-c指定配置文件
supervisord -c /etc/supervisord.conf
# 如果不指定配置文件
supervisord

# 那么配置文件会依次再下面的文件夹中寻找
# $CWD/supervisord.conf
# $CWD/etc/supervisord.conf
# /etc/supervisord.conf
```

### 更新配置文件

当我们修改配置后，为了使新的配置生效，我们需要通知supervisor是新配置文件生效，我们使用下面的命令：

```bash
# 使新的配置文件生效
supervisorctl update
```

### 其他命令

当supervisor运行后，管理他就不能用supervisord了，而是supervisorctl。下面是一些常用的运行命令

```bash
# 控制所有进程
supervisorctl start all
supervisorctl stop all
supervisorctl restart all
# 控制目标进程
supervisorctl stop shadowsocks
supervisorctl start shadowsocks
supervisorctl restart shadowsocks
```

### 查看日志

```bash
supervisorctl tail -f shadowsocks stdout
supervisorctl tail -f shadowsocks stderr
```

## 开机自启动

```bash
vi /etc/rc.local

# 在exit前添加以下内容
supervisord -c /etc/supervisord.conf
```

## 扩展

### 开始web服务管理

如此配置后我们就可以在浏览器中输入`127.0.0.1:9001`来管理supervisor托管的任务。

如果将回环地址127.0.0.1换为服务器的IP地址，就可以可以远程管理supervisor了。

```
# 在配置文件后加上服务器配置信息
[inet_http_server]
port = 127.0.0.1:9001
username = user
password = 123

# 最后不要忘了reload使之生效！
supervisorctl reload
```


### 在centos7中把supervisor配置为服务

将下面的代码保存到 `/etc/systemd/system/supervisord.service`，注意配置文件的路径

```bash
[Unit]
Description=Supervisor process control system for UNIX
Documentation=http://supervisord.org
After=network.target

[Service]
ExecStart=/usr/bin/supervisord -n -c /etc/supervisord.conf
ExecStop=/usr/bin/supervisorctl $OPTIONS shutdown
ExecReload=/usr/bin/supervisorctl $OPTIONS reload
KillMode=process
Restart=on-failure
RestartSec=50s

[Install]
WantedBy=multi-user.target
```

然后启动服务：

```
systemctl daemon-reload
systemctl enable supervisord
systemctl restart supervisord
systemctl status supervisord
```