---
title: "centos实时网速监控工具nload"
categories: [ "服务器技术" ]
tags: [ "centos","nload","netspeed" ]
draft: false
slug: "nload_centos"
date: "2014-12-27 04:06:00"
url: "nload_centos.html"
---

[nload][1]是一个很小巧的工具，用来监控当前系统的网速


<!--more-->


## 安装

```bash
wget http://www.roland-riegel.de/nload/nload-0.7.4.tar.gz
tar -xf nload-0.7.4.tar.gz
cd nload-0.7.4
./configure
make && make install
```

## 使用

### 查看帮助

```bash
nload -h

Options:
-a period       Sets the length in seconds of the time window for average
                calculation.
                平均计算时间长度
                Default is 300.
-i max_scaling  Specifies the 100% mark in kBit/s of the graph indicating the
                incoming bandwidth usage. Ignored if max_scaling is 0 or the
                switch -m is given.
                指定入口流量最大值 单位是kBit/s
                Default is 10240.
-m              Show multiple devices at a time; no traffic graphs.
                同时显示多个设备统计信息
-o max_scaling  Same as -i but for the graph indicating the outgoing bandwidth
                usage.
                和-i相同，但是是出口流量
                Default is 10240.
-t interval     Determines the refresh interval of the display in milliseconds.
                刷新间隔
                Default is 500.
-u h|b|k|m|g    Sets the type of unit used for the display of traffic numbers.
   H|B|K|M|G    h: auto, b: Bit/s, k: kBit/s, m: MBit/s etc.
                H: auto, B: Byte/s, K: kByte/s, M: MByte/s etc.
                显示单位
                Default is h.
-U h|b|k|m|g    Same as -u, but for a total amount of data (without "/s").
   H|B|K|M|G    Default is H.
                和-u相同，但是为总流量
devices         Network devices to use.
                指定网卡
                Default is to use all auto-detected devices.
--help
-h              Print this help.

example: nload -t 200 -i 1024 -o 128 -U M

The options above can also be changed at run time by pressing the 'F2' key.
运行时使用F2可以查看此页
```

### 图形查看

```bash
nload -t 200 -i 1024 -o 128 -U M
# 左右键可以在多个网卡之间切换
```

### 网卡流量

```bash
nload eth0
nload -m
```


  [1]: http://www.roland-riegel.de/nload/