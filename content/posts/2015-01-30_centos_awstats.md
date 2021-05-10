---
title: "centos-awstats简单安装教程"
categories: [ "服务器技术" ]
tags: [ "centos","aws","awstats" ]
draft: false
slug: "centos_awstats"
date: "2015-01-30 08:08:00"
url: "centos_awstats.html"
---

本文关键字:awstats linux centos nginx

Awstats官网：[http://www.awstats.org/][1]

发行协议：GNU GPL

> Awstats is short for Advanced Web statistic.

Awstats的功能老高就不多介绍了，总之老高觉得比百度站长好用太多，不过在安装此软件过程中老高也遇到了不少坑，网上的安装教程看的人眼花缭乱，针对centos的教程更是无法直视，于是造成了此篇文章诞生，看完你也许会觉得安装Awstats确实不难。

再介绍之前，为了给大家一个宏观的概念，我给大家梳理了以下安装过程。一共有两条主线任务：配置日志；配置Nginx服务器。前者为后者的展示提供数据，后者负责通知前者计算数据。

注意：以下内容以配置老高的域名为例。


<!--more-->


![Awstats安装说明][2]

## 下载源码

下载地址：[http://www.awstats.org/#DOWNLOAD][3]

老高推荐下载**Last stable**

```bash
# Last stable version is 7.3 - 2014-07-23 07:50
# 2015-01-30 21:50:29
cd /tmp
wget http://heanet.dl.sourceforge.net/project/awstats/AWStats/7.3/awstats-7.3.tar.gz
tar -zxf awstats-7.3.tar.gz

# rename
mv awstats-7.3 awstats

# move to /usr/share/
# 至于为什么要移至/usr/share/，后文有原因
mv awstats /usr/share/
cd /usr/share/awstats

# dir tree
tree -L 1
.
├── docs
├── README.TXT
├── tools           # tools即我们需要的计算工具
└── wwwroot         # wwwroot是我们需要的web视图
```

## 配置计算工具

接下来我们初始化我们需要计算的内容

首先需要明确几个文件的位置：

1. 日志文件(eg. `/var/log/nginx/xxx.access.log`)
2. 配置文件(eg. `/etc/awstats`)
3. 工具类文件(eg. `/usr/share/awstats/tools`)

下面我们进入tools文件夹，开始走第一条线。

### 新建配置文件

```bash
cd tools
perl awstats_configure.pl
Do you want to continue setup from this NON standard directory [yN] ? y

# 是否配置web服务器
Config file path ('none' to skip web server setup):
>none

# 是否新建一个配置文件
Do you want me to build a new AWStats config/profile
file (required if first install) [y/N] ? y

Your web site, virtual server or profile name:
>www.phpgao.com # 你要分析的域名

# 这一步选择你的配置文件路径
In which directory do you plan to store your config file(s) ?
Default: /etc/awstats
Directory path to store config file(s) (Enter for default):
>Enter

# 继续回车，配置完毕
# 接下来自定义我们的配置文件

vim /etc/awstats/awstats.www.phpgao.com.conf
```

### 配置日志路径

如果要统计那就必须指定统计日志的路径。

AWS为我们提供了很多种办法，帮助我们找到正确的文件。

配置路径的变量一般在line 50左右，在它的上面可以找到编写路径的提示。

```bash
%YYYY # 可以代替4位年份
%YY   # 可以代替2位年份
%MM   # 可以代替2位月份
%DD   # 可以代替2位天数
%HH   # 可以代替2位小时

%YYYY-%MM-%DD-%HH  # 在程序执行的时候自动以当前日期2015-01-31-10(此时)替换，是不是很方便？
```

指定日志文件一般有两种常见的形式

```bash
# 指定单个文件
LogFile="/var/log/nginx/logs/xxxx.access.log"

# 指定多个文件
# 需要使用/usr/share/awstats/tools/logresolvemerge.pl脚本指定你需要的所有日志文件，然后再文本最后加入一个字符 "|"，表示合并所有文件结果。

LogFile="/usr/share/awstats/tools/logresolvemerge.pl /var/log/nginx/logs/xxx.access.log  /var/log/nginx/logs/yyy.access.log|"

```

### 配置日志格式

一般nginx的日志格式不需要配置，如果需要变动，请修改大约line 122，系统预置了四种格式供选择。

```bash
# 1 - Apache or Lotus Notes/Domino native combined log format (NCSA combined/XLF/ELF log format)
# 2 - IIS or ISA format (IIS W3C log format). See FAQ-COM115 For ISA.
# 3 - Webstar native log format.
# 4 - Apache or Squid native common log format (NCSA common/CLF log format)
LogFormat=1
```

### 指定计算结果目录

系统计算出的结果是需要保存在磁盘上的，如果自定义这个路径呢？

在配置文件line 203附近，你可以找到系统的默认配置。

```bash
DirData="/var/lib/awstats"
# custom
DirData="your_path_to/awstats"
# 然后我们需要对这个路径的权限做一些处理

chown -R www:www awstats
# www用户我们稍后创建
```

### 网页端刷新数据

如果需要在网页里能够主动刷新数据，请找到约line 239，并修改内容。

![主动刷新数据][4]

```bash
# modify
AllowToUpdateStatsFromBrowser=1
```

### 开启地理位置支持

如果想得到如下结果，需要安装并开启GEO支持。

![地理位置支持][5]

```bash
# 安装GEO支持
yum install GeoIP GeoIP-data GeoIP-devel perl-Geo-IP -y

# 安装完毕后移至 配置文件约 line 1429
# uncomment
LoadPlugin="geoip GEOIP_STANDARD  /usr/share/GeoIP/GeoIP.dat"
```

### 解决中文乱码

如果安装AWS后搜索处中文乱码，请修改约line 1204，删除注释。

```bash
LoadPlugin="decodeutfkeys"
```

### 测试并更新配置

解决了以上问题，我们就可以开始动手测试配置是否正确了。

```bash
perl /usr/share/awstats/wwwroot/cgi-bin/awstats.pl -config=www.phpgao.com

------------------------------------------
From data in log file "/usr/share/awstats/tools/logresolvemerge.pl /var/log/nginx/logs/xxx.access.log  /var/log/nginx/logs/yyy.access.log|"...
Phase 1 : First bypass old records, searching new record...
Searching new records from beginning of log file...
Phase 2 : Now process new records (Flush history on disk after 20000 hosts)...
Jumped lines in file: 0
Parsed lines in file: 42302
 Found 42 dropped records,
 Found 0 comments,
 Found 0 blank records,
 Found 404 corrupted records,
 Found 41801 old records,
 Found 55 new qualified records.
------------------------------------------
# 如果看到这种信息说明你的配置文件没有问题，否则请检查。
```

至此，配置完毕

## 配置服务器

### 配置nginx虚拟主机

如果你仔细浏览`/usr/share/awstats/tools`目录，会发现一个nginx文件夹，没错，源码包已经为我们提供了nginx的虚拟主机配置文件了，我们只需要简单的修改一下即可！

> 老高把虚拟主机都放到vhosts文件夹下，然后再主配置文件里使用`include vhost/*.conf`引用，是不是很方便！

```bash
# 配置虚拟主机
cp awstats-nginx.conf /etc/nginx/vhost/awstats.conf

vim /etc/nginx/vhosts/awstats.conf
```

以下是老高给出的配置示例文件，为统计查看绑定的域名为aws.phpgao.com，端口80。

```nginx
server {
    listen  80;
    server_name aws.phpgao.com;
    access_log /var/log/nginx/awstats.access_log main;
    error_log /var/log/nginx/awstats.error_log info;
    root /var/www/localhost/htdocs;
    index index.html;

    # Restrict access
    # auth_basic "Restricted";
    # auth_basic_user_file /etc/awstats/htpasswd;


    # Static awstats files: HTML files stored in DOCUMENT_ROOT/awstats/
    location /awstats/classes/ {
        alias /usr/share/awstats/wwwroot/classes/;
    }

    location /awstats/css/ {
        alias /usr/share/awstats/wwwroot/css/;
    }

    location /awstats/icon/ {
        alias /usr/share/awstats/wwwroot/icon/;
    }

    location /awstats-icon/ {
        alias /usr/share/awstats/wwwroot/icon/;
    }

    location /awstats/js/ {
        alias /usr/share/awstats/wwwroot/js/;
    }

    location /awstatsicons/ {
        alias /usr/share/awstats/wwwroot/icon/;
    }
    
    # Dynamic stats.
    location ~ ^/cgi-bin/(awredir|awstats)\.pl {
        gzip off;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME /usr/share/awstats/wwwroot/cgi-bin/fcgi.php;
        fastcgi_param X_SCRIPT_FILENAME /usr/share/awstats/wwwroot$fastcgi_script_name;
        fastcgi_param X_SCRIPT_NAME $fastcgi_script_name;
        include fastcgi_params;
    }

    #Rewrite
    location ~ ^/([a-z0-9-_\.]+)$ {
        return 301 $scheme://aws.phpgao.com/cgi-bin/awstats.pl?config=$1;
    }
}
```

默认网址 ： `http://aws.phpgao.com/cgi-bin/awstats.pl?config=www.phpgao.com`
伪静态后 ： `http://aws.phpgao.com/www.phpgao.com`


### 开启认证

由于AWS没有提供认证模块，所以我们使用nginx的认证，我们需要为nginx提供一个认证文件。

有两种办法生成此文件

1.htpasswd命令

```bash
# 路径可以自己指定
htpasswd -c /etc/awstats/htpasswd username:password
# 删除文件中Restrict access的注释
    Restrict access
    auth_basic "Restricted";
    auth_basic_user_file /etc/awstats/htpasswd;
```

2.PHP生成

```php
$password = 'www.phpgao.com'; 
// 对密码进行加密 
$password = crypt($password, base64_encode($password)); 
// 获得加密后的密码 
echo $password;
```

然后将得出的密码手动写入文件/etc/awstats/htpasswd，格式如下

```
username1:xucqMk13TfooE
username2:YXTfb3xWKOMBM
```

效果如下：

![密码验证][6]

完

Reference

 - http://kamisama.me/2013/03/20/install-configure-and-protect-awstats-for-multiple-nginx-vhost-on-debian/
 - http://www.fancycedar.info/2013/06/apache-nginx-htpasswd/


  [1]: http://www.awstats.org/
  [2]: http://ww3.sinaimg.cn/large/6735b7fatw1eorvt807nlj20oz08e0th.jpg
  [3]: http://www.awstats.org/#DOWNLOAD
  [4]: http://ww3.sinaimg.cn/large/6735b7fagw1eosij24axej20av01t74e.jpg
  [5]: http://ww3.sinaimg.cn/large/6735b7fagw1eosi1a6i0pj20ln07776j.jpg
  [6]: http://ww4.sinaimg.cn/large/6735b7fagw1eoskm7mn7mj209y0763yu.jpg