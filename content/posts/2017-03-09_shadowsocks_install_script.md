---
title: "一个ss的安装脚本"
categories: [ "服务器技术" ]
tags: [ "shell","shadowsocks" ]
draft: false
slug: "shadowsocks_install_script"
date: "2017-03-09 07:30:00"
url: "shadowsocks_install_script.html"
---

这个脚本是写给小白和我用的，可以直接在**centos**上可以执行，使用方法很简单。

更新：


这个脚本已经很旧了，请移步 
 - [Centos7 系统一键安装 Shadowsocks 和 KCPTUN 加速][1]
 - [Ubuntu 系统一键安装 Shadowsocks 和 KCPTUN 加速][2]



<!--more-->


```
usage :
	 help                        显示帮助
	 change_ssh_port [2222]      修改随机ssh端口
	 install_ss_py               安装ss-python
	 install_ss_libv             安装ss-libv
	 add_server_py               添加ss-python服务
	 add_server_libv             添加ss-libv服务
```

随机SSH端口

```
# 端口不指定就会随机一个
sh ss-installer.sh change_ssh_port
sh ss-installer.sh change_ssh_port 64532
```

代码贴在[gist][3]上了，欢迎拍砖！


  [1]: https://blog.phpgao.com/auto_install_shadowsocks_and_kcptun.html
  [2]: https://blog.phpgao.com/auto_install_shadowsocks_and_kcptun-html.html
  [3]: https://gist.github.com/phpgao/49bb9d31e4371598c16b97f186b152f6