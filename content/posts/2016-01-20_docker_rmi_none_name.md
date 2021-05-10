---
title: "docker-批量删除名字为none的镜像"
categories: [ "代码人生" ]
tags: [ "docker","none" ]
draft: false
slug: "docker_rmi_none_name"
date: "2016-01-20 03:58:00"
url: "docker_rmi_none_name.html"
---

下面代码的意思是批量停止已经Exited的容器，然后删除之，最后删除，因为名称为none的镜像可能还没有退出，这样做也保险一点。


```
docker stop $(docker ps -a -q -f status=exited)
docker rm -v $(docker ps -a -q -f status=exited)
docker images|grep none|awk '{print $3 }'|xargs docker rmi
```

如果要直接删除所有的容器，可以这样：

```
docker kill $(docker ps -a -q)
docker rm -v $(docker ps -a -q)
```