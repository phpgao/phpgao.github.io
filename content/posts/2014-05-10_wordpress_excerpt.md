---
title: "WordPress首页文章不显示全文的方法"
categories: [ "性能优化" ]
tags: [ "wordpress","index" ]
draft: false
slug: "wordpress_excerpt"
date: "2014-05-10 03:50:00"
url: "wordpress_excerpt.html"
---

博客用的国外的主题，对中文处理不是太好。尤其是首页会把文章的全部内容都输出，使首页变得很长，对于我这种爱贴代码的程序猿来说，这个病，必须治！

解决办法有三种：

 1. more标签 
 2. 自己写方法截取文字
 3. the_excerpt()

## more标签

以下引用sumile_ting 的话：

这种方法是在每次写文章时在文章中插入一个More标签。

添加More标签的方法：

> 写文章时，在需要作为摘要的地方点击编辑器中的More标签，或在HTML编辑状态加加入<!–more–>，或者直接按Alt+Shift+T,3种方法任选其一。

这种方法使用起来太麻烦，要求每一篇文章都得手动加，一不小心可能就忘记了。还不如修改主题模板来的方便快捷。

## 自造方法

自己写方法必须对function.php和content.php做改写，先找到

    <?php the_content(); ?>
    
the_content()方法返回了文章的内容，处理一下就可以限制字数了，具体方法请自查。

但是对于我还是不合适，因为此时文章还没有被插件渲染，文章内的代码会被直接以纯文本的形式展示，很不和谐。
所以这个方案，pass

## the_excerpt()

此方法是wordpress显示文章摘要的API，效果与the_content() 一样，只是这个函数对中文支持不好，需要处理一下

以下是步骤：

方法2的the_content()修改为the_excerpt()
在function下加入以下代码

    function custom_excerpt_length( $length ) {
        return 200;
    }
    add_filter( 'excerpt_length', 'custom_excerpt_length', 999 );

给readmore加个链接，同样在functions.php中添加：

    function new_excerpt_more( $more ) {
        return '<a href="' . get_permalink() . '">阅读全文</a>';
    }
    add_filter('excerpt_more', 'new_excerpt_more');
 
完美解决

参考：

http://sumile.blog.hexun.com/63924812_d.html

http://www.gemshe.com/fashion/suneyeglass/wordpress%E7%9A%84%E6%96%87%E7%AB%A0%E5%BD%A2%E5%BC%8Fget_post_format/

http://www.seanstudio.com/archives/2558

http://www.daxiawp.com/change-to-excerpt.html