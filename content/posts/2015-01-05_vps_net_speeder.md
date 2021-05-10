---
title: "使用net_speeder加速你的VPS"
categories: [ "服务器技术" ]
tags: [ "vps","optimize","speedup","net_speeder" ]
draft: false
slug: "vps_net_speeder"
date: "2015-01-05 09:56:00"
url: "vps_net_speeder.html"
---

转自:http://www.im1987.com/post/853.html

老高安装了以后480P是不卡了，720P还是有点力不从心啊~

另外推荐一下锐速，应该还是会有一点效果的。


<!--more-->


项目作者:[http://www.snooda.com/read/324][1]

[googe项目库][2]

下面的Centos一键安装代码转载于hostloc的[@lazyzhu][3]

```bash
#!/bin/sh
 
# Set Linux PATH Environment Variables
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
 
# Check If You Are Root
if [ $(id -u) != "0" ]; then
    clear
    echo -e "\033[31m Error: You must be root to run this script! \033[0m"
    exit 1
fi
 
if [ $(arch) == x86_64 ]; then
    OSB=x86_64
elif [ $(arch) == i686 ]; then
    OSB=i386
else
    echo "\033[31m Error: Unable to Determine OS Bit. \033[0m"
    exit 1
fi
if egrep -q "5.*" /etc/issue; then
    OST=5
    wget http://dl.fedoraproject.org/pub/epel/5/${OSB}/epel-release-5-4.noarch.rpm
elif egrep -q "6.*" /etc/issue; then
    OST=6
    wget http://dl.fedoraproject.org/pub/epel/6/${OSB}/epel-release-6-8.noarch.rpm
else
    echo "\033[31m Error: Unable to Determine OS Version. \033[0m"
    exit 1
fi
 
rpm -Uvh epel-release*rpm
yum install -y libnet libnet-devel libpcap libpcap-devel gcc
 
wget http://net-speeder.googlecode.com/files/net_speeder-v0.1.tar.gz -O -|tar xz
cd net_speeder
if [ -f /proc/user_beancounters ] || [ -d /proc/bc ]; then
    sh build.sh -DCOOKED
    INTERFACE=venet0
else
    sh build.sh
    INTERFACE=eth0
fi
 
NS_PATH=/usr/local/net_speeder
mkdir -p $NS_PATH
cp -Rf net_speeder $NS_PATH
 
echo -e "\033[36m net_speeder installed. \033[0m"
echo -e "\033[36m Usage: nohup ${NS_PATH}/net_speeder $INTERFACE \"ip\" >/dev/null 2>&1 & \033[0m"
```

将代码保存为`net_speeder_lazyinstall.sh`，然后执行.

```bash
# do installation
sh your_path_to/net_speeder_lazyinstall.sh
```

Ubuntu:

```
# root用户
apt-get install libnet1 libnet1-dev libpcap0.8 libpcap0.8-dev -y
git clone https://github.com/snooda/net-speeder.git
sh build.sh

mv net_speeder /usr/local/bin/
```

手动启动：

```bash
nohup net_speeder venet0 "ip" >/dev/null 2>&1 &
```

最后加入启动脚本

```
echo 'nohup net_speeder venet0 "ip" >/dev/null 2>&1 &' >> /etc/rc.local
```

成功标识：(DUP!)

![success sign][4]


推荐文章：

[使用shadowsocks轻松搭建FQ环境][5]

[VPS安全之SSH设置][6]

[路由器折腾手记(自动翻墙)][7]


References:


  [1]: http://www.snooda.com/read/324
  [2]: https://github.com/snooda/net-speeder
  [3]: http://lazyzhu.com/
  [4]: https://blog.phpgao.com/usr/uploads/2015/01/3591770760.png
  [5]: https://blog.phpgao.com/shadowsocks_on_linux.html
  [6]: https://blog.phpgao.com/vps_ssh.html
  [7]: https://blog.phpgao.com/carzy_router.html