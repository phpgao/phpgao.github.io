---
title: "当用户不存在时执行adduser"
categories: [ "代码人生" ]
tags: [ "docker","adduser" ]
draft: false
slug: "adduser_if_user_do_not_exist"
date: "2016-10-21 07:49:00"
url: "adduser_if_user_do_not_exist.html"
---

使用了`id`工具，构建docker镜像的时候这一句很有用！

```
id -u laogao &>/dev/null || useradd -u 30105 laogao
```