---
title: "Centos7-系统一键安装-Shadowsocks-和-KCPTUN-加速"
categories: [ "代码人生" ]
tags: [ "centos","shadowsocks" ]
draft: false
slug: "auto_install_shadowsocks_and_kcptun"
date: "2017-08-03 04:17:00"
url: "auto_install_shadowsocks_and_kcptun.html"
---

新安装的centos7系统可以使用下面的安装脚本一键安装$$，如果嫌复制粘贴麻烦，可以到[脚本地址][1]下载

## 安装命令

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/phpgao/my_scripts/master/code/ss_for_centos.sh)"

# 或者

sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/phpgao/my_scripts/master/code/ss_for_centos.sh)"

```

安装完毕后查看ss配置

```
cat /etc/ss-config.json
```

查看kcptun配置

```
/etc/kcp-config.json
```

如果想要修改，使用vi命令即可。修改完记得重启服务才能生效！

```
# 重启ss服务
systemctl restart shadowsocks-server

# 重启kcptun服务
systemctl restart kcp-server
```

<!--more-->


## 卸载命令

```bash
# 卸载ss
systemctl stop shadowsocks-server
systemctl disable shadowsocks-server
rm -f /etc/ss-config.json /etc/systemd/system/shadowsocks-server.service
systemctl daemon-reload


# 卸载kcptun
systemctl stop kcp-server
systemctl disable kcp-server
rm -f /etc/kcp-config.json /etc/systemd/system/kcp-server.service
systemctl daemon-reload

```


  [1]: https://github.com/phpgao/my_scripts/tree/master/code