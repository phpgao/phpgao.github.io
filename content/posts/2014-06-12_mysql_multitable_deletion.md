---
title: "mysql的多表删除"
categories: [ "代码人生" ]
tags: [ "mysql" ]
draft: false
slug: "mysql_multitable_deletion"
date: "2014-06-12 06:30:00"
url: "mysql_multitable_deletion.html"
---

mysql默认是禁用多表删除语句的，之前的做法都是先把条件缓存，然后逐条执行

    delete from a where a.xxx in (123123,123123);
    delete from b where b.xxxx in (123123,123123);

如果写成下面这种绝对是错误的！ 

    delete * from a,b where xxxx in  ...

今天突然发现原来mysql也是可以一条sql删除多表数据的，下面来分析一下 多表删除其实思路还是比较简单的，首先条件就是多个表（一般就两个表，织梦的文章信息存在3个表里，删除起来很麻烦）有关联 其次，删除条件一般都是由关联的这一行组成的，也可能不是，没有关系，我们先写一个试试： 

    DELETE p.*, pp.* 
    FROM pw_threads p, pw_tmsgs pp 
    WHERE p.tid = pp.tid 
    AND p.tid in(SELECT tid FROM pw_threads WHERE postdate>UNIX_TIMESTAMP('2014-06-12 00:00:00') and fid=136)

逻辑都通着，但是会报错 

> You can't specify target table 'A' for update in FROM clause 原因是:
In general, you cannot modify a table and select from the same table in a subquery.

所以你需要把子查询改为临时表中，所以最终的查询就是 

    DELETE p.*, pp.* 
    FROM pw_threads p, pw_tmsgs pp 
    WHERE p.tid = pp.tid 
    AND p.tid in(SELECT tmp.tid FROM (SELECT * FROM pw_threads) as tmp WHERE tmp.postdate>UNIX_TIMESTAMP('2014-06-12 00:00:00') and tmp.fid=136)