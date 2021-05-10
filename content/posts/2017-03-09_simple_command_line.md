---
title: "一句话命令"
categories: [ "代码人生" ]
tags: [ "code","snippet" ]
draft: false
slug: "simple_command_line"
date: "2017-03-09 09:16:00"
url: "simple_command_line.html"
---

记录一些比较简单又懒得记得命令


<!--more-->

## bash 类型

```bash
# 循环遍历下载，不下载父页面，文件已存在的话不下载
wget -r -nc -np https://yum.dockerproject.org/repo/main/centos/

# 分片20M 上传
bypy -vvv -s 20M upload

# mac用代理启动单个app
proxychains open -a Airy.app

# mac下查看端口占用
nettop -nm tcp

# brew

brew uninstall zsh
brew services list

brew services start
brew services stop
brew services restart

# 关闭所有磁盘的索引服务
sudo mdutil -a -i off
```

## IP

```bash
# 获取IP(国内)
curl cip.cc
curl haoip.cn
curl ip.taobao.com/service/getIpInfo2.php?ip=myip
curl myip.ipip.net
curl ns1.dnspod.net:6666
curl myip.ipip.net/s
curl myip.ipip.net/json
curl members.3322.org/dyndns/getip

# 国外(json)
curl ipinfo.io
curl ipinfo.io/ip
curl ipinfo.io/org
curl ipinfo.io/8.8.8.8/org
curl api.ipify.org/?format=json

curl myexternalip.com/raw
curl whatismyip.akamai.com
curl ifconfig.co/ip
curl ifconfig.me
curl ifconfig.me/ip
curl api.ipify.org
```

## Python

```python
# 如果是相对路径，获取绝对路径
if not os.path.isabs(some_path):
     print os.path.realpath(some_path)

# 获取脚本文件的路径
print os.path.split(os.path.realpath(__file__))[0]
```