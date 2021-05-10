---
title: "wordpress自动生成文章目录"
categories: [ "性能优化" ]
tags: [ "content","post" ]
draft: false
slug: "auto_index"
date: "2014-03-16 15:20:00"
url: "auto_index.html"
---

看过百度百科的同学都知道，如果百科文章比较长，那么他会生成一个目录，不仅增强了用户体验，还为文章引入了锚点内链，对SEO优化起到了十分积极的作用。

请不要以为这个功能很复杂，其实目录功能只是牵扯到一个正则，一个JS和一个CSS而已，对于我们wordpress来说，很多插件已经实现了此功能。

老高@PHPer使用的是一款名叫Content Index for WordPress的国人自制插件，功能强大，使用方便。

## Content Index for WordPress

### 安装 

wordpress内置的插件管理器搜索"Content Index for WordPress"，安装即可。 

### 使用 

安装后请至wordpress后台->设置->文章内索引： 老高@PHPer使用如下配置： 

![配置][2]

### 开启 

插件默认对所有文章关闭，如果你想在某一篇文章中使用目录，请在编辑日志页面勾选 

> 在此文章/页面中开启
### Tips 

也许你已经发现了，PHPer@老高使用了锚点之间的平滑滚动，如果你也希望实现此效果，请将下面的代码引入你的文章模板中

    $(document).ready(function() {
        $('a[href*=#]').click(function() {
            if (location.pathname.replace(/^\//, '') == this.pathname.replace(/^\//, '') && location.hostname == this.hostname) {
                var $target = $(this.hash);
                $target = $target.length && $target || $('[name=' + this.hash.slice(1) + ']');
                if ($target.length) {
                    var targetOffset = $target.offset().top;
                    $('html,body').animate({
                        scrollTop: targetOffset
                    },
                    1000);
                    return false;
                }
            }
        });
    });

### 注意 可能你还没发现，上面的代码会出现一些小问题，比如你的网站留言也会出现某些锚点，点击它们将会出现很多意想不到的效果，所以我们需要将锚点控制在目录范围内 因此，我们需要在第二行的Jquery代码做一些修改 

    $('a[href*=#]').click(function()
    
改为 

    $('#content-index-contents a[href*=#]').click(function() {

### 再次注意 
经测试发现，如果你的<h>标签中出现如,(半角逗号).(半角逗号)和空格的话，平滑滚动会失效。 所以可能需要将,(半角逗号).(半角逗号)和空格替换为全角标点或者英文的下划线即可。 


## 另一款插件名叫TOC+，也很简单好用，请百度之！


  [1]: https://blog.phpgao.com/wp-content/uploads/2014/05/article_catalogue.png
  [2]: https://blog.phpgao.com/usr/uploads/2014/07/3297766703.png