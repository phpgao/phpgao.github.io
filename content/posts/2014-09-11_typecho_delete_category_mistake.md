---
title: "修复typecho删除分类后统计文章数的错误"
categories: [ "代码人生" ]
tags: [ "sql","typecho" ]
draft: false
slug: "typecho_delete_category_mistake"
date: "2014-09-11 15:28:00"
url: "typecho_delete_category_mistake.html"
---

博客的文章渐渐多了起来，之前随意的分类就需要好好想整理一下。

为了图方便，直接在数据库里做了替换查询，不料typecho和WP一样，都会在metas表里存着分类和tag的统计信息，统计了这个分类下有多少篇文章，这个统计信息显示在分类和TAG管理页面。

也就是说，如果正常再文章编辑里修改分类并保存，统计信息会走一加一减这个过程，而暴力数据库修改不会触发。

而这种混乱导致老高的瞬间变身不搞不舒服斯基，下面我们一步一步来修复这个统计信息。

首先，找到所有分类（标签同理）

    SELECT mid FROM typecho_metas AS m WHERE m.type = 'category';

然后，找到分类关系表，统计文章数

    SELECT r.mid,
           count(cid)
    FROM typecho_relationships as r
    WHERE r.mid IN
        (SELECT mid
         FROM typecho_metas AS m
         WHERE m.type='category')
    GROUP BY r.mid;

再次，我们提取出分类名称

    SELECT r.mid,
           m.name,
           count(cid)
    FROM typecho_relationships as r
    LEFT JOIN typecho_metas as m ON m.mid=r.mid
    WHERE r.mid IN
        (SELECT mid
         FROM typecho_metas AS m
         WHERE m.type='category')
    GROUP BY r.mid;

最后，以上一个查询为基础修改原始表metas

    UPDATE typecho_metas,
      (SELECT r.mid AS mid,
              count(cid) AS COUNT
       FROM typecho_relationships AS r
       WHERE r.mid IN
           (SELECT mid
            FROM typecho_metas AS m
            WHERE m.type='category')
       GROUP BY r.mid) AS tmp
    SET typecho_metas.COUNT = tmp.COUNT
    WHERE tmp.mid = typecho_metas.mid;

上面的SQL也表明了如果使用两张表跟新数据。

突然发现使用分类管理的合并到功能也可以，但是没我这个快 B)