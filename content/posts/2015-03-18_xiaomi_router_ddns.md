---
title: "小米路由器mini折腾之DDNS动态域名解析篇"
categories: [ "路由器" ]
tags: [ "router","xiaomi","pandorabox","openwrt","ddns","3322","花生壳","myddns" ]
draft: false
slug: "xiaomi_router_ddns"
date: "2015-03-18 01:14:00"
url: "xiaomi_router_ddns.html"
---

老高家里用的是联通10M宽带，500/年。自从小米路由器mini刷了pandorabox后无法使用官方的APP管理设备，于是想到了使用openwrt自带的DDNS，即动态DNS来远程管理家里的路由器。


<!--more-->


## 准备工作

想要顺利的使用DDNS，需要满足以下条件：

1. 路由器拨号后需要有公网IP
1. 运营商没有完全屏蔽端口

针对第一个问题，老高以前家里的拨号就是内网IP，如10.0.0.6，相当于家里的路由器处于一个很大的局域网中。外部的请求只能到整个小区的公网IP，所以也就不能使用传统的方式搭建DDNS。但办法不是没有，像要穿透内网买一个小棒子(花生棒)就能解决。老高用了一段时间，感觉不如直接连接稳定，所以就去联通营业厅申请了公网IP，虽然需要实名认证，但是咱也不干什么违法的事，所以就申请了。然后经过我两个月期间不断的投诉，终于拿到了公网IP。本身就是很简单改个配置的事，联通这样做很伤人心啊！

![使用花生棒穿透内网搭建DDNS][1]

针对第二个问题，需要用端口扫描工具(如ScanPort)扫描一下你的公网IP，端口范围选择1-65535。然后看看到底开放了哪些，老高这里就比较悲剧，只开放了一个端口，要么给ssh，要么给luci。真尼玛。。。

> ps.端口一样可以去运营商申请开放！

## 如何配置DDNS

如果你的条件满足以上，就可以开始配置了。

下面老高以3322和花生壳为例，简单的介绍一下配置过程。

### 开通账号

移步至 http://www.pubyun.com/ 注册一个免费账号，然后创建一个动态域名。

![在3322注册一个免费域名][2]

花生壳需要公网版免费帐号，登陆网址在此：

https://console.oray.com/passport/login?url=http%3A%2F%2Fhsk.oray.com%2Fconsole%2Fmanage%2F

![在花生壳注册免费域名][3]

### 配置动态域名信息

首先进入动态DNS配置界面

![动态DNS配置界面][4]

然后在设置对应的信息

![DDNS设置][5]

下面老高给出详细的设置：

1. 启用打勾
1. Event interface :  WAN
1. 服务：自定义
1. 更新的URL：
    - 花生壳：http://[USERNAME]:[PASSWORD]@ddns.oray.com/ph/update?hostname=[DOMAIN]&myip=[IP]
    - 3322: http://[USERNAME]:[PASSWORD]@members.3322.org/dyndns/update?system=dyndns&hostname=[DOMAIN]&myip=[IP]&wildcard=OFF
1. 主机名：申请到的域名，不带http
1. 用户名：对应账户登陆账号
1. 密码：对应账户登陆密码
1. IP地址来源：网络
1. 网络：WAN
1. 检查IP变动时间间隔：10
1. 时间单位：分钟
1. 强制更新间隔：32
1. 强制更新的时间单位：小时

> ps.你可以同时配置多个DDNS，以避免单点故障

### 如何配置多个动态域名解析

细心的同学可能已经发现，配置的最下面有一个添加按钮

![多个动态域名解析][6]

在这里自定义新的ddns名称，如myddns2，点击新建。这样你就同时使用了两个DDNS服务！

### 如何使配置生效

1. 重启路由器
2. 使用命令行

```bash
# myddns是默认的ddns名称，
/bin/sh /usr/lib/ddns/dynamic_dns_updater.sh myddns 0

# 开启第二个动态域名解析
/bin/sh /usr/lib/ddns/dynamic_dns_updater.sh myddns2 0
```

如果一切都没有问题，过几分钟后再ping你的域名，就会发现IP已经更新为路由器的IP！

## 远程管理路由器

还记得刚开始我们使用工具扫描到的端口吗？这里我们就需要配置ssh或者luci的端口到开放的端口上去！这样我们就可以远程管理我们的路由器了。

### 修改luci的端口

运营商一般都会屏蔽80端口，所以想要远程登陆web后台，就必须修改端口号，下面老高将器端口设置为8080。

说是修改luci界面的端口，其实上修改的是openwrt轻量级的web服务器uhttp的服务端口，其配置文件存于`/etc/config/uhttpd`，下面我们试着修改它。

> ps.uhttp的配置说明在此 [http://wiki.openwrt.org/doc/uci/uhttpd][7]

```bash
vi /etc/config/uhttpd

# 原始数据
config uhttpd 'main'
        list listen_http '0.0.0.0:80'
        list listen_http '[::]:80'
        ...
        ...

# 修改为
config uhttpd 'main'
        list listen_http '0.0.0.0:8080'
        list listen_http '[::]:8080'
        ...
        ...
```

修改保存后，我们重启uhttpd服务。

```bash
/etc/init.d/uhttpd restart
```

#### 安全须知

- 上面我们修改了原来的端口到8080，以后访问后台界面就必须在URL后面加端口号，但是如果遇到了网络黑客扫描了你的端口，黑客就能访问你的登陆页面，同时如果你有设置了一个**弱密码**，这样一来黑客就能轻易的登陆并控制你的网络，所以如果开放了外网的访问，一定要给后台设置一个**强密码**。

- 如果在公网，最好通过https访问路由器，这样就不会让各种敏感信息在互联网上“飞来飞去”了。

#### 更多配置

uhttp还支持多实例绑定多端口，这样就不必修改原来的配置，只需要添加一个新的实例配置即可实现修改端口，并且不会影响到内网的访问。

### 修改ssh端口

远程ssh一定是大家更需要的，luci实质上也就是执行了一些预设的脚本，而ssh对路由器的控制是100%的。openwrt使用轻量的dropbear，下面老高修改外网可以访问的端口为8888。

> ps. dropbear的配置说明在此 [http://wiki.openwrt.org/doc/uci/dropbear][8]

```bash
vi /etc/config/dropbear

# 修改为
config dropbear
        option PasswordAuth 'on'
        option Interface 'lan'
        option GatewayPorts 'on'
        option Port '22'

config dropbear
        option PasswordAuth 'on'
        option Interface 'wan'
        option GatewayPorts 'on'
        option Port '8888'
```

修改保存后，我们重启dropbear服务。

```bash
/etc/init.d/dropbear restart
```

现在我们就可以通过外网IP远程登录ssh。

```
ssh root@yourname.phpgao.net -p8888
```

  [1]: http://ww3.sinaimg.cn/large/6735b7fagw1eq9n2guxu3j20db0gtwgs.jpg
  [2]: http://ww2.sinaimg.cn/large/6735b7fagw1eq9o3kana3j20r10baq5y.jpg
  [3]: http://ww3.sinaimg.cn/large/6735b7fagw1eqayhfus1bj20zm08wabk.jpg
  [4]: http://ww3.sinaimg.cn/large/6735b7fagw1eqasuaf30bj204h07r74i.jpg
  [5]: http://ww2.sinaimg.cn/large/6735b7fagw1eqasvkuirsj20f80j40uc.jpg
  [6]: http://ww4.sinaimg.cn/large/6735b7fagw1eqaybha361j20bh033748.jpg
  [7]: http://wiki.openwrt.org/doc/uci/uhttpd
  [8]: http://wiki.openwrt.org/doc/uci/dropbear