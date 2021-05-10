---
title: "让typecho支持emoji"
categories: [ "服务器技术" ]
tags: [ "mysql","utf8","utf8mb4" ]
draft: false
slug: "typecho_with_emoji"
date: "2016-12-07 06:45:00"
url: "typecho_with_emoji.html"
---

这年头文章里加个emoji很正常吧，但是typecho貌似之前没有考虑到，没有原生支持emoji，但是这怎么能难倒我们勤劳的码农呢！

![doge][1]

<!--more-->


> utf8mb4需要 Mysql5.5.3+ 版本的支持，mb4 = most bytes 4，utf8是utf8mb4的子集，所以不需要额外的转换。

## 新站

如果是新建站，那么一定要在建立数据库的时候就制定当前数据库的编码为utf8mb4 + utf8mb4_general_ci。这样默认建表的编码就全部为utf8mb4了，最后修改config.inc.php文件中：

```php
/** 定义数据库参数 */
$db = new Typecho_Db('Pdo_Mysql', 'typecho_');
$db->addServer(array (
  'host' => '1.2.3.4',
  'user' => 'root',
  'password' => 'phpgao',
  'charset' => 'utf8mb4',  # ----> 这一行如果不是utf8mb4请修改为 utf8mb4
  'port' => '3306',
  'database' => 'phpgao',
), Typecho_Db::READ | Typecho_Db::WRITE);
Typecho_Db::set($db);
```

完事儿

## 旧站

首先修改数据表编码：

```mysql
# typecho_baidusubmit是老高百度文章提交的表
alter table typecho_baidusubmit convert to character set utf8mb4 collate utf8mb4_general_ci;
alter table typecho_comments convert to character set utf8mb4 collate utf8mb4_general_ci;
alter table typecho_contents convert to character set utf8mb4 collate utf8mb4_general_ci;
alter table typecho_fields convert to character set utf8mb4 collate utf8mb4_general_ci;
alter table typecho_metas convert to character set utf8mb4 collate utf8mb4_general_ci;
alter table typecho_options convert to character set utf8mb4 collate utf8mb4_general_ci;
alter table typecho_relationships convert to character set utf8mb4 collate utf8mb4_general_ci;
alter table typecho_users convert to character set utf8mb4 collate utf8mb4_general_ci;
```

然后也需要修改config.inc.php文件，方法同新站，不再赘述。

至此，你的站点就已经支持emoji了，但是在不支持emoji的平台下表情会变为方框。

## mysql

添加到mysql.ini文件中，可以让utf8mb4更好地工作。

```ini
[client]  
default-character-set=utf8mb4  
  
[mysqld]  
character-set-server = utf8mb4  
collation-server = utf8mb4_general_ci  
init_connect='SET NAMES utf8mb4'  
skip-character-set-client-handshake = true  
  
[mysql]  
default-character-set = utf8mb4  
```

## 补充

github等一大波版本工具都支持emoji了，你还在等什么！?

[EMOJI CHEAT SHEET][2]


Rerfer:

 - [MySQL数据库字符集由utf8修改为utf8mb4一例][3]


  [1]: https://blog.phpgao.com/usr/uploads/2016/12/2007228227.jpg
  [2]: http://www.webpagefx.com/tools/emoji-cheat-sheet/
  [3]: http://blog.csdn.net/yumushui/article/details/49153525