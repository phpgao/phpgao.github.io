---
title: "穿墙神器frp的安装与配置"
categories: [ "服务器技术" ]
tags: [ "frp" ]
draft: false
slug: "frp_config"
date: "2018-07-13 11:39:00"
url: "frp_config.html"
---

如题


<!--more-->


## 客户端

### centos安装脚本

```
version=`curl --silent "https://api.github.com/repos/fatedier/frp/releases/latest" | grep -Po '"tag_name": "\K.*?(?=")'`
v=`echo $version|grep -Po 'v\K(.*)'`
curl -sSL https://github.com/fatedier/frp/releases/download/$version/frp_"$v"_linux_amd64.tar.gz|tar zx -C /tmp/ -x frp_"$v"_linux_amd64/frpc frp_"$v"_linux_amd64/frpc.ini --strip-components 1
mv /tmp/frpc /usr/local/bin/frpc
mkdir /etc/frp/
mv /tmp/frpc.ini /etc/frp/


cat <<EOF > /etc/systemd/system/frpc.service
[Unit]
Description=frpc daemon
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/frpc -c /etc/frp/frpc.ini
ExecReload=/usr/local/bin/frpc -c /etc/frp/frpc.ini reload
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
EOF
```

### 服务管理

```bash
systemctl enable frpc
systemctl restart frpc
systemctl status frpc
```

## 服务端

### centos安装脚本

```
version=`curl --silent "https://api.github.com/repos/fatedier/frp/releases/latest" | grep -Po '"tag_name": "\K.*?(?=")'`
v=`echo $version|grep -Po 'v\K(.*)'`
curl -sSL https://github.com/fatedier/frp/releases/download/$version/frp_"$v"_linux_amd64.tar.gz|tar zx -C /tmp/ -x frp_"$v"_linux_amd64/frps frp_"$v"_linux_amd64/frps.ini --strip-components 1
mv /tmp/frps /usr/local/bin/frps
mkdir /etc/frp/
mv /tmp/frps.ini /etc/frp/

cat <<EOF > /etc/systemd/system/frps.service
[Unit]
Description=frps daemon
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/frps -c /etc/frp/frps.ini
ExecReload=/usr/local/bin/frps -c /etc/frp/frps.ini reload
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
EOF
```

### 服务管理

```bash
systemctl enable frps
systemctl restart frps
systemctl status frps
```