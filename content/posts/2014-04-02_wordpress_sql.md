---
title: "WP常用SQL总结"
categories: [ "性能优化" ]
tags: [ "sql","wordpress" ]
draft: false
slug: "wordpress_sql"
date: "2014-04-02 15:42:00"
url: "wordpress_sql.html"
---

<span style="color: #ff0000;">运行SQL一定要备份，以防万一！</span>

### 先来看看每个表的功能吧 

wp\_commentmeta：存储评论的元数据 
wp\_comments：存储评论 
wp\_links：存储友情链接（Blogroll） 
wp\_options：存储WordPress系统选项和插件、主题配置 
wp\_postmeta：存储文章（包括页面、上传文件、修订）的元数据 
wp\_posts：存储文章（包括页面、上传文件、修订） 
wp\_terms：存储每个目录、标签 
wp\_term\_relationships：存储每个文章、链接和对应分类的关系 
wp\_term\_taxonomy：存储每个目录、标签所对应的分类 
wp\_usermeta：存储用户的元数据 
wp_users：存储用户信息   

### 删除残留垃圾数据 

使用WordPress经常换主题删主题，装插件删插件很正常，但是简单的删除并不彻底，数据库会有残留，多余的数据保留在post_meta表格里，久而久之就成了一堆可观的垃圾。可使用下面的SQL语句来清除不需要的postmeta值。有益于加快数据库运行速度，减小数据。 执行SQL语句 

    DELETE FROM wp_postmeta WHERE meta_key = '_edit_lock';
    DELETE FROM wp_postmeta WHERE meta_key = '_edit_last';

### 删除草稿修订版本 

在WordPress后台中编辑文章时，系统会自动保存许多修订的副本。过多的修订记录会加重数据库的负担并造成了资源的浪费。数据库越来越庞大，增加了数据检索影响页面的加载时间。 执行SQL语句 

    DELETE a,b,c FROM wp_posts a
    LEFT JOIN wp_term_relationships b ON (a.ID = b.object_id)
    LEFT JOIN wp_postmeta c ON (a.ID = c.post_id)
    WHERE a.post_type = 'revision'

注意：此方法将删除所有的文章的所有修订版，包括相关的meta数据。 

### 处理未使用的标签 

在WordPress数据库中，如果你使用一个查询语句手动来删除旧的文章，旧的标签却仍然会保留并在你的标签云/列表中出现。你可以使用下面的方法识别未使用的标签并将它删除。 执行SQL语句 

    SELECT * FROM wp_terms wt
    INNER JOIN wp_term_taxonomy wtt ON wt.term_id=wtt.term_id
    INNER JOIN wp_term_relationships wtr ON wtr.term_taxonomy_id=wtt.term_taxonomy_id
    LEFT JOIN wp_posts wp ON wp.ID=wtr.object_id
    WHERE taxonomy='post_tag'
    AND ID IS null
    AND NOT EXISTS(SELECT * From wp_terms wt2
    INNER JOIN wp_term_taxonomy wtt2 ON wt2.term_id=wtt2.term_id WHERE wtt2.parent=wt.term_id) ORDER BY name;

### 修复和优化 

执行完所有的语句之后，全选所有的表，然后选择“修复表”和“优化表”。 (来源于互联网)