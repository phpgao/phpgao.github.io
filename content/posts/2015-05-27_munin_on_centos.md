---
title: "在centos上安装Munin监控服务器运行状态"
categories: [ "服务器技术" ]
tags: [ "munin","munin-node" ]
draft: false
slug: "munin_on_centos"
date: "2015-05-27 13:48:00"
url: "munin_on_centos.html"
---

老高的服务器在搬瓦工上跑着，虽然后台有各种监控信息，但是要想查看还是必须登录后再点击很多次才能看到，很麻烦，于是通过Google找到了这个小巧的系统监控软件 -- [Munin][1]。这个软件在系统中部署很简单，几行代码就能搞定！

> 2014-11-24: Munin 2.0.25 is released. 

> [Munin手册地址][2]


<!--more-->


![munin logo][3]

## 安装

在安装之前请确保已安装EPEL，如果没有，你可以参考[centos安装EPEL][4]来安装Munin

```bash
yum --enablerepo=epel install munin munin-common munin-node rrdtool
```

> 通过安装提示我们可以发现，munin主要依靠perl来工作。

安装完毕后系统会有如下改变：

```bash
/etc/munin/munin.conf : Munin master(服务器端) 配置文件.
/etc/cron.d/munin : Munin 设置crontab计划任务文件.
/etc/httpd/conf.d/munin.conf : Munin Apache 配置文件.
/var/log/munin : Munin log日志目录.
/var/www/html/munin : Munin 网站目录.
/etc/munin/munin-node.conf : Munin Node节点配置文件(指定监控哪些节点).
/etc/munin/plugins.conf : Munin plugins插件配置文件.
/etc/rc.d/init.d/munin-node : Munin启动脚本
/etc/munin/plugin-conf.d/munin-node munin-node插件配置.
```

## 配置监控端

### 计划任务

系统已经为我们创建好了计划任务。

```bash
cat /etc/cron.d/munin

#
# cron-jobs for munin
#

MAILTO=root

*/5 * * * *     munin test -x /usr/bin/munin-cron && /usr/bin/munin-cron
```

### 配置监控选项

接下来我们要告诉程序我们需要监控那些主机，配置文件位于`/etc/munin/munin.conf`

```
vim /etc/munin/munin.conf

# 其中 localhost已经被我们配置好了
[localhost]
    address 127.0.0.1
    use_node_name yes
[new domai]
    address 45.62.xxx.2xx
    use_node_name yes

# use_node_name yes 的意思是使用[]内的名字作为当前IP的主机名
```

### nginx服务

我们创建一个虚拟主机，绑定域名munin.phpgao.com，然后将根目绑定录到`/var/www/html/munin/`即可！

> 为了保证刚开始的正常服务，老高注释了auth段配置，后面会讲到如何使用。

```nginx
server {
    server_name munin.phpgao.com;
    # Restrict access
    # auth_basic "Restricted";
    # auth_basic_user_file /var/www/html/munin/.htpasswd;
    location / {
        alias /var/www/html/munin/;   #路径地址为 munin.conf 配置 htmldir 的路径。
    }
}
```

### 文件

监控端每天会产生四个日志文件，历史文件会被压缩，在/var/log/munin目录下：

```
munin-graph.log
munin-html.log
munin-limits.log
munin-update.log
```

截图：

![监控端文件][5]


----------


## 配置被监控端

### 配置文件

被监控端只需要安装`munin-node`即可，用来**产生日志**供监控端使用。

> 监控端与被监控端可以是同一台主机，也可以不同。

我们先看看node的配置文件，文件比较长，截取部分讲解。

```
cat /etc/munin/munin-node.conf

log_file /var/log/munin-node/munin-node.log #产生日志的的目录

allow ^127\.0\.0\.1$    # IP白名单，如果不是监控本机，请修改为监控端的IP。
allow ^222\.222\.111\.111$ # 假设监控端的IP为222.222.111.111

port 4949 # 监听的端口，为监控端服务
```

### 运行

```bash
# 重启、启动服务
service munin-node restart

# 查看是否启用
netstat -lapn|grep 4949

# 运行 netstat -lapn|grep 4949 可以看到perl在监听此端口
# tcp   0      0 :::4949  :::*   LISTEN      22849/perl

## 加入开机启动脚本

chkconfig --level 35 munin-node on
```

## 等待数据

当以上的配置都搞定后，等待片刻，后台就会出现数据了！

![munin后台][6]

## 高级设置

### 配置权限

还记得nginx配置的时候用到的验证功能吗？ [centos搭建awstats简单安装教程][7]里面也用到了。

此功能使用了apache的`htpasswd `命令，如果遇到bash提示`command not found`，请使用`yum install httpd`安装。

再次我们建立一个用户名 phpgao，密码为 hello 的验证文件

```bash
htpasswd -c /var/www/html/munin/.htpasswd phpgao
# 再输入两遍 hello 即可保存成功
```

然后我们反注释掉刚才的nginx配置

```nginx
server {
    server_name munin.phpgao.com;
    # Restrict access
    auth_basic "Restricted";
    auth_basic_user_file /var/www/html/munin/.htpasswd;
    location / {
        alias /var/www/html/munin/;   #路径地址为 munin.conf 配置 htmldir 的路径。
    }
}
```

最后重载nginx

```
nginx -t
service nginx reload
```

![nginx重启][8]


----------


### munin插件

以memcached插件为例，插件主页[Memcached-Munin-Plugin][9]。

- **下载插件**

```bash
cd /tmp
git clone https://github.com/mhwest13/Memcached-Munin-Plugin.git
```

- 拷贝至插件目录

```bash
cd Memcached-Munin-Plugin
cp memcached_multi_ /usr/share/munin/plugins/memcached_multi_
```

下面这条命令可以检测插件是否正确安装成功。

`munin-node-configure --suggest | grep memcached_multi_`

- 编辑munin-node配置文件

```bash
vim /etc/munin/plugin-conf.d/munin-node
```

添加以下内容

```
[memcached_multi_*] 
env.host 127.0.0.1 
env.port 11211 
env.timescale 3 
env.cmds get set delete incr decr touch 
env.leitime -1
```

- 创建软连接

```bash
ln -s '/usr/share/munin/plugins/memcached_multi_' '/etc/munin/plugins/memcached_multi_bytes'
ln -s '/usr/share/munin/plugins/memcached_multi_' '/etc/munin/plugins/memcached_multi_commands'
ln -s '/usr/share/munin/plugins/memcached_multi_' '/etc/munin/plugins/memcached_multi_conns'
ln -s '/usr/share/munin/plugins/memcached_multi_' '/etc/munin/plugins/memcached_multi_evictions'
ln -s '/usr/share/munin/plugins/memcached_multi_' '/etc/munin/plugins/memcached_multi_items'
ln -s '/usr/share/munin/plugins/memcached_multi_' '/etc/munin/plugins/memcached_multi_memory'
ln -s '/usr/share/munin/plugins/memcached_multi_' '/etc/munin/plugins/memcached_multi_unfetched'
```

- 重启服务

```bash
service munin-node restart
```

至此插件配置完成，稍等片刻会有数据了！

![memcached统计数据][10]

reference

http://www.dabu.info/centos-install-munin-monitor-nginx-apache.html


  [1]: http://munin-monitoring.org
  [2]: http://guide.munin-monitoring.org/en/latest/index.html
  [3]: https://blog.phpgao.com/usr/uploads/2015/05/3758689789.png
  [4]: https://blog.phpgao.com/centos_install_epel.html
  [5]: https://blog.phpgao.com/usr/uploads/2015/05/2938213385.png
  [6]: https://blog.phpgao.com/usr/uploads/2015/05/4241233739.png
  [7]: https://blog.phpgao.com/centos_awstats.html
  [8]: https://blog.phpgao.com/usr/uploads/2015/05/1238987972.png
  [9]: https://github.com/mhwest13/Memcached-Munin-Plugin
  [10]: https://blog.phpgao.com/usr/uploads/2015/05/3440072102.png