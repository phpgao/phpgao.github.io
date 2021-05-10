---
title: "openwrt中br-lan,eth0,eth0.1,eth0.2"
categories: [ "服务器技术" ]
tags: [ "Linux","router","openwrt","network" ]
draft: false
slug: "openwrt-interface"
date: "2015-06-02 11:07:00"
url: "openwrt-interface.html"
---

openwrt的接口真是多，老高一下子都看晕了，不过仔细理一理，还是能够分辨的。

首先盗openwrt-wiki中的一张图，这张图对理解这些接口很重要！

![asus-internals-default][1]


<!--more-->


## 网络配置

### 有线网配置

openwrt的有线网络配置文件位于`/etc/config/network`，老高的配置如下：

```
config interface 'loopback'
	option ifname 'lo'
	option proto 'static'
	option ipaddr '127.0.0.1'
	option netmask '255.0.0.0'

config globals 'globals'
	option ula_prefix 'auto'

config interface 'lan'
	option ifname 'eth0.1'
	option force_link '1'
	option type 'bridge'
	option proto 'static'
	option ipaddr '192.168.1.1'
	option netmask '255.255.255.0'
	option ip6assign '60'
	option macaddr '64:09:80:05:e1:bb'

config interface 'wan'
	option ifname 'eth0.2'
	option _orig_ifname 'eth0.2'
	option _orig_bridge 'false'
	option proto 'pppoe'
	option macaddr '64:09:80:05:e1:bc'
	option username '11100026295'
	option password '380926'

config interface 'wan6'
	option _orig_ifname '@wan'
	option _orig_bridge 'false'
	option proto '6to4'

config switch
	option name 'mt762x'
	option reset '1'
	option enable_vlan '1'

config switch_vlan
	option device 'mt762x'
	option vlan '1'
	option ports '0 1 2 3 5 6t'

config switch_vlan
	option device 'mt762x'
	option vlan '2'
	option ports '4 6t'
```

### 无线网络配置

openwrt的无线网络配置文件位于`/etc/config/wireless`，老高的配置如下：

```
config wifi-device 'rai0'
	option type 'mt7612'
	option mode '14'
	option channel 'auto'
	option txpower '100'
	option ht '20+40+80'
	option country 'US'
	option disabled '0'

config wifi-iface
	option device 'rai0'
	option network 'lan'
	option mode 'ap'
	option ssid 'BYBY_WORLD'
	option encryption 'psk2'
	option key 'xxx'

config wifi-device 'ra0'
	option type 'rt2860v2'
	option mode '9'
	option channel 'auto'
	option txpower '100'
	option ht '40'
	option country 'US'
	option disabled '0'

config wifi-iface
	option device 'ra0'
	option network 'lan'
	option mode 'ap'
	option wps 'pbc'
	option ssid 'HELLO_WORLD'
	option encryption 'psk2'
	option key 'xxx'
```

### 整理和搜索

乍一看老高都晕了，真么多啊！赶紧Google之，看有没有什么发现！

[交换机手册(Switch Documentation)][2]

[Linux 网络接口(Network Interfaces)][3]

读完这两篇文章后貌似有所觉悟，先记下来理一理！

## pppoe-wan

虚拟设备，他就是常见的拨号宽带上网，需要有ISP提供的用户名密码，连接后方可激此接口！

## lo

虚拟设备，自身的回环网设备。

## ra0 rai0

这两个是成对出现，一看就知道是无线设备，它们各自对应一个SSID，分别是2.4G和5G。

## 虚拟局域网

### 开启虚拟局域网

对应 `config switch`

### 划分子网

你可以在`网络->交换机`中找到！

![vlan设置][4]

对照着图下面的配置应该很容易就懂了。

```
config switch_vlan
	option device 'mt762x'
	option vlan '1'
	option ports '0 1 2 3 5 6t'

config switch_vlan
	option device 'mt762x'
	option vlan '2'
	option ports '4 6t'
```

## eth0

eth0是一块物理网卡。eth0.1 eth0.2都是从此设备上虚拟出来的。

eth0.1 是vlan1分出的lan口。

eth0.2 是vlan分出的wan口。

## br-lan

虚拟设备，用于LAN口设备桥接，可以用`brctl show`查看使用情况。

```
~ brctl show

bridge name	bridge id		STP enabled	interfaces
br-lan		7fff.64098005e1bb	no		eth0.1 rai0 ra0
```

br-lan = eth0.1 + rai0 + ra0，即将有线LAN口和无线网统一划分为 **LAN**，便于管理！


更多资料

 - [How can I tell whether a network interface is physical (device) or
   virtual (alias)?][5]
 - [PandoraBox的配置网络文件...基础内容，高手忽略][6]

  [1]: https://blog.phpgao.com/usr/uploads/2015/06/3406911994.png
  [2]: http://wiki.openwrt.org/zh-cn/doc/uci/network/switch
  [3]: http://wiki.openwrt.org/zh-cn/doc/networking/network.interfaces
  [4]: https://blog.phpgao.com/usr/uploads/2015/06/4203665553.png
  [5]: http://unix.stackexchange.com/questions/57309/how-can-i-tell-whether-a-network-interface-is-physical-device-or-virtual-alia
  [6]: http://www.right.com.cn/forum/forum.php?mod=viewthread&tid=160760&page=2&mobile=no