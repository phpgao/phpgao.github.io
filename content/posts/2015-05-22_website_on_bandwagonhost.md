---
title: "使用搬瓦工的VPS搭建博客网站"
categories: [ "服务器技术" ]
tags: [ "bandwagonhost","ftp","domain" ]
draft: false
slug: "website_on_bandwagonhost"
date: "2015-05-22 02:07:00"
url: "website_on_bandwagonhost.html"
---

想必大家买了VPS后一定都不想闲着。。。搭个博客算是最简单实用了。

但是基本上大家在网上下载到的一键安装包都没有**后台管理**功能，如果想多绑定几个域名都不知道怎么操作，更别提搭建多个站点了。

下面老高就介绍一种简单实用的网站搭建方法，搭建出的网站速度快，安全性高，还支持各种优化配置。

最重要的是操作基本都是傻瓜式的，不需要很多web技能也可以完整的配置下来，而且基本上就成了半个运维了，是不是很厉害！

> 像老高这样的**老司机**，整个装下来再加上安全设置最多5分钟搞定，相信你一定也可以！

搭建过程中如果有什么问题，请留言交流！


![kangle][1]

<!--more-->


## 准备

以下是安装需求

### 系统

目前仅支持 **centos6** (至于是minimal还是其他版本都行)，但最好全新安装的，省的与之前安装的软件起冲突。

> centos还支持搬瓦工后台一键安装shadowsocks，经济又实用！

### 硬件

内存至少要512MB，否则网站可能跑不起来。

### 域名

为了让大家更好的访问你的网站，请准备一个域名。

 - 免费域名

如果使用免费域名，老高推荐`.tk`，注册很简单，缺点是百度不收录。

 - 收费域名

如果使用收费域名，老高建议不要在国内注册，并且千万不要注册`.cn`。

如果要推荐的话，老高推荐到[namesilo][2]注册一个com国际域名，使用优惠码`bug`，一年只要$7.99，还支持支付宝。

为什么推荐namesilo呢？请参考[正在将域名转出至namesilo][3]。

注册完域名，把你的要解析的域名绑定到 VPS的IP地址，后面会用到。

## 开始安装

### 更新系统

做一次更新，我们需要让系统随时保持最新状态

```bash
yum update -y
```

### 执行安装

下面我们一键安装所有需要的软件

```
yum -y install wget;wget http://download.kangleweb.com/easypanel/ep.sh -O ep.sh;sh ep.sh
```

等安装完成，我们的服务器就具备了所有**WEB服务器**的功能。

## 完成后的操作

安装完成后，我们需要先做一些安全配置

### 修改mysql的root密码

```
# 将密码修改为 '老高'
mysqladmin -u root password "老高"
```

## 后台管理地址

### 管理面板网址

`http://服务器ip:3312/admin/`

![管理后台登陆界面][4]

默认用户名 `admin`
默认密码  `kangle`

> ps.登录之后建议立即修改默认的信息，帐号管理 --> 修改帐号信息

### 初始化系统

首先，进入左侧服务器设置

![服务器设置][5]

我们只需要设置第二行，mysql配置。

将刚才设置的root用户信息输入

![配置mysql用户][6]

然后点击左侧`服务器管理 --> 初始化服务器`，确定即可！

## 新建网站

现在我们来新建网站，点击`网站管理 --> 新增网站`

![如何添加网站][7]

按照老高霸气的示意图配置完毕，点击确定即可！


需要注意的是，一旦网站建立完成，很多账户密码就已经绑定好了！如果以后需要再网页中填写信息，如数据库或者FTP信息，请参考以下对应信息。

```
FTP用户名    刚刚设置的用户名
FTP密码      刚刚设置的密码

数据库地址    localhost
数据库名称    刚刚设置的用户名
数据库用户名    刚刚设置的用户名
数据库密码    刚刚设置的密码
数据库端口    3306
```

## 管理网站

网站创建完毕后，我们就可以使用刚才的用户名密码管理我们的子站了！

为什么说子站呢？因为用这个方法，理论上你可以在这个VPS上建立**N多**基于`PHP`站点！很爽der!

> 子站管理地址

> http://服务器ip:3312/vhost/

![子站登录界面][8]

### 后台功能

登录成功后，你就能够发现一块新大陆！老高已经把常用的功能框出来了。

![后台界面][9]

### 绑定域名

刚刚提到的域名操作会在此用上，你一定想不到绑定域名就这么简单！

![绑定域名操作][10]

### 上传文件

网站文件请使用FTP上传至`wwwroot`文件夹下，上传完毕确保文件`wwwroot/index.php`能够被找到！

## 高级操作

### 让kangle支持memcache

默认kangle是没有安装php的memcache模块的，我们需要手动安装并启用。

- 安装memcached

```
# 安装memcache以及php模块
yum install memcached php-pecl-memcache -y
# 设置memcache开机启动
chkconfig memcached --level 35 on
# 运行memcache
service memcached start
```

- 编辑INI文件

下面我们配置PHP.ini

![配置PHP.INI][11]

```
# 在右侧另起新行添加以下配置
[Memcached]
extension=memcached.so
```

- 重启以生效

现在我们重启服务器使其生效

```
service kangle restart
```


  [1]: https://blog.phpgao.com/usr/uploads/2015/11/3949848852.gif
  [2]: http://www.namesilo.com
  [3]: https://blog.phpgao.com/transfer_to_namesilo.html
  [4]: https://blog.phpgao.com/usr/uploads/2015/05/2704960517.jpeg
  [5]: https://blog.phpgao.com/usr/uploads/2015/05/1213759337.png
  [6]: https://blog.phpgao.com/usr/uploads/2015/05/2447250928.png
  [7]: https://blog.phpgao.com/usr/uploads/2015/05/3419173325.jpeg
  [8]: https://blog.phpgao.com/usr/uploads/2015/05/3033835331.jpeg
  [9]: https://blog.phpgao.com/usr/uploads/2015/05/3940603567.jpeg
  [10]: https://blog.phpgao.com/usr/uploads/2015/05/98027142.jpeg
  [11]: https://blog.phpgao.com/usr/uploads/2015/05/2729403563.png