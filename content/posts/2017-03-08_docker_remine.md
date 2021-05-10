---
title: "使用docker运行redmine"
categories: [ "代码人生" ]
tags: [ "docker","ruby","redmine" ]
draft: false
slug: "docker_remine"
date: "2017-03-08 13:41:00"
url: "docker_remine.html"
---

网上的都不太靠谱，还是自己过一遍吧！


<!--more-->

## 安装过程

```
docker pull redmine:3
docker pull mysql:5

mkdir -p /home/docker_data/mysql

docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=phpgao -e MYSQL_DATABASE=redmine -v /home/docker_data/mysql:/var/lib/mysql mysql:5

# ! 进入数据库将redmine库的编码改为gb2312

docker run -d --name redmine --link mysql:mysql -p 8080:3000 redmine:3
```

## 几个问题

 - 默认用户名密码都是 **admin**
 - 进入系统后时区显示有问题，官方默认时区不是中国，可以自己改一下
 - 备份和恢复的文件夹要注意，目测上传的文件在**/usr/src/redmine/files/**，配置文件在**/usr/src/redmine/config**
 - !{{child_pages(depth=2)}}
 - 更多宏自己搜
 - https://github.com/themondays/Dwarf
