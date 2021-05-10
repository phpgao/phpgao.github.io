---
title: "PHPCMS登陆流程"
categories: [ "代码人生" ]
tags: [ "phpcms","login","phpsso" ]
draft: false
slug: "phpcms_login"
date: "2014-11-05 06:38:00"
url: "phpcms_login.html"
---

1. 调用`phpcms/modules/member/index.php`中login。

2. 读取`caches/configs/system.php`中phpsso的配置。

3. 调用`phpcms/modules/member/classes/client.class.php`的_ps_post()发送登录信息。

4. 该请求被发送到`phpsso_server/phpcms/modules/phpsso/index.php`的login方法。

5. phpsso读取数据库配置`phpsso_server/caches/configs/database.php`，连接数据库，执行登陆逻辑

6. 返回登录结果