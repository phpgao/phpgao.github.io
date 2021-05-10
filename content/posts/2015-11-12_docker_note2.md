---
title: "docker使用笔记-II----multi-port-shadowsocks"
categories: [ "服务器技术" ]
tags: [ "docker","note" ]
draft: false
slug: "docker_note2"
date: "2015-11-12 05:46:00"
url: "docker_note2.html"
---

![docker logo][1]

[上一节][2]我们建立了简单的容器，这一届我们进一步探索docker的功能


<!--more-->


## 暴露多个端口

很多情况下，我们将队列与缓存部署在一起，即 beanstalkd + redis，都是用默认端口，11300和6379。

### Dockerfile

```docker
FROM ubuntu:14.04
MAINTAINER LAO GAO <endoffight#gmail.com>

# 下面一段话是为了加速update
RUN sed -i "s/archive\.ubuntu\.com/mirrors\.163\.com/g" /etc/apt/sources.list
#RUN sed -i "s/archive\.ubuntu\.com/mirrors\.aliyun\.com/g" /etc/apt/sources.list
#RUN sed -i "s/archive\.ubuntu\.com/tw\.archive\.ubuntu\.com/g" /etc/apt/sources.list

RUN apt-get -qq update
RUN apt-get install redis-server beanstalkd -y
EXPOSE      6379 11300
CMD ["beanstalkd","-p","11300"]
CMD ["/usr/bin/redis-server"]
```

> ps. 我们在Dockerfile中暴露了两个端口，所以我们在之后运行的时候需要用-p重复2次指定多个端口映射，或者使用-P将端口随机映射至宿主机器。

### 运行实例

```bash
sudo docker build --rm=true -t laogao/bst_redis .

# 暴露了多个端口
sudo docker run -d -p 6379:6379 -p 11300:11300 laogao/br
```

## Shadowsocks

我们可以把ss放到docker中运行

```docker
FROM ubuntu:14.04
MAINTAINER LAO GAO <endoffight#gmail.com>

RUN sed -i "s/archive\.ubuntu\.com/mirrors\.163\.com/g" /etc/apt/sources.list
#RUN sed -i "s/archive\.ubuntu\.com/mirrors\.aliyun\.com/g" /etc/apt/sources.list
#RUN sed -i "s/archive\.ubuntu\.com/tw\.archive\.ubuntu\.com/g" /etc/apt/sources.list


RUN apt-get update
RUN apt-get install python-pip -y
RUN pip install shadowsocks

ENTRYPOINT ["/usr/local/bin/ssserver"]
```

### Build

```bash
sudo docker build --rm=true -t laogao/ss -< ss.docker
```

### RUN

```bash
# 我们实际运行的命令
/usr/local/bin/ssserver --workers 10 --pid-file /tmp/ss.pid --log-file /tmp/ss.log --user nobody -c /root/config.json -v -d start

# 使用ENTRYPOINT 后使用docker运行
sudo docker run -d -p 1234:1234 laogao/ss  --workers 10 --pid-file /tmp/ss.pid --log-file /tmp/ss.log --user nobody -c /root/config.json -v -d start

# 或者直接指定运行参数
sudo docker run -d -p 1234:1234 laogao/ss -s 0.0.0.0 -p 1234 -k yourpasswd -m rc4-md5
```

> 不要忘了把你的防火墙对应端口打开哦！


  [1]: https://blog.phpgao.com/usr/uploads/2015/11/2256355596.png
  [2]: https://blog.phpgao.com/docker-note1.html