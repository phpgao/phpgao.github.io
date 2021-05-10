---
title: "centos升级内核版本以支持overlay2"
categories: [ "代码人生" ]
tags: [ "Linux","docker","kernel" ]
draft: false
slug: "update_linux_kernel"
date: "2017-11-30 05:00:00"
url: "update_linux_kernel.html"
---

搬瓦工新入的KVM架构的机器，默认内核版本是`4.10.4`，如果是本地用ISO安装的系统，默认内核版本可能更低，比如`3.10.0`，如果我们想要获取到最新的Linux特性，那么就必须升级内核版本到最新，我们可以用很简单的几个命令就可做到。


<!--more-->


> PS.用下面的命令查看当前系统的内核版本，多个平台适用

```bash
uname -sr
# 老高的不同的机器返回的不同结果
# Linux 4.10.4-1.el7.elrepo.x86_64
# Linux 3.10.0-327.el7.x86_64
# Darwin 17.2.0
```

centos7下，我们用下面的命令升级内核


```bash
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org \
&& rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm \
&& yum clean all \
&& yum --enablerepo=elrepo-kernel install kernel-ml -y \
&& grub2-set-default 0
```

**至此**，我们重启系统就已经完成了内核的升级，内核升级是完成了，我们就可以使用一些新特性了，比如在此老高以docker的新文件驱动`overlay2`为例，使用一下Linux kernel 4.0以后才支持的overlay2（Linux kernel 3.18以后才支持的叫overlayFS）。同时请确保docker的服务端版本不低于1.12，否则无法支持。

> 需要注意的是，更换驱动后之前保存镜像和容器的目录地址会变化，之前所有镜像和容器将变的不可见！（比如`/var/lib/docker/devicemapper`将会变为`/var/lib/docker/overlay2`，但是前者不会被删除，是否意味着可以恢复？）

```bash
# 我们可以先检查Linux是否加载了overlay模块
lsmod|grep over
# 如果有返回则说明已经加载
# 如果确定内核是最新那么可以执行
modinfo overlay
# 此处应该有类似/lib/modules/4.10.4-1.el7.elrepo.x86_64/kernel/fs/overlayfs/overlay.ko
# 最后我们添加overlay模块即可
modprobe overlay

# 如果是新装的系统，那么在重启前把selinux关闭是个明智的选择
setenforce 0 && sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux

vi /etc/docker/daemon.json
```

在`daemon.json`文件中添加如下配置，如果之前有配置，请将二者合并。

```json
{
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
```

重启docker服务

```
systemctl restart docker

docker info

Server Version: 1.12.6
Storage Driver: overlay2
 Backing Filesystem: extfs
Logging Driver: journald
```

至此我们成功了启用了overlay文件驱动。更多信息可以访问[Use the OverlayFS storage driver][1]已了解更多。

> 目前overlay2已经是最新版docker的默认文件驱动，取代了之前的AUFS和devicemapper的地位。


  [1]: https://docs.docker.com/engine/userguide/storagedriver/overlayfs-driver/