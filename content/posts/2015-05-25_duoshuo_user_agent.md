---
title: "让多说支持UA的显示"
categories: [ "代码人生" ]
tags: [ "ua","duoshuo" ]
draft: false
slug: "duoshuo_user_agent"
date: "2015-05-25 04:34:00"
url: "duoshuo_user_agent.html"
---

代码由 huhuhuhu 提供

地址 [huhu呼呼 / Make DUOSHUO Show UA][1]

老高的博客目前的评论系统使用了 多说 + typecho多说插件，最近才发现了多说还可以支持显示UA，动手吧。


<!--more-->


## 准备工作

### CSS

CSS很短，可以直接写在源码里

```
span.this_ua {
	background-color: #ccc!important;
	border-radius: 4px;
	padding: 0 5px!important;
	margin: 0 1px!important;
	border: 1px solid #BBB!important;
	color: #fff;

	/*text-transform: Capitalize!important;
	float: right!important;
	line-height: 18px!important;*/
}
.this_ua.platform.Windows{
	background-color: #39b3d7!important;
	border-color: #46b8da!important;
}
.this_ua.platform.Linux {
	background-color: #3A3A3A!important;
	border-color: #1F1F1F!important;
}
.this_ua.platform.Android {
	background-color: #00C47D!important;
	border-color: #01B171!important;
}
.this_ua.browser.Chrome{
	background-color: #5cb85c!important;
	border-color: #4cae4c!important;
}
.this_ua.browser.Firefox{
	background-color: #f0ad4e!important;
	border-color: #eea236!important;
}
.this_ua.browser.IE{
	background-color: #428bca!important;
	border-color: #357ebd!important;
}
.this_ua.browser.Opera{
	background-color: #d9534f!important;
	border-color: #d43f3a!important;
}
```

### Make-DUOSHUO-Show-UA

很多同学发现显示有问题

```
<!--多说UA显示|此段js必须放在加载embed.js之后，运行显示评论代码之前[start]-->
<script type="text/javascript">
            if (typeof DUOSHUO !== 'undefined')hookDUOSHUO_tp();
            else $('[src="http://static.duoshuo.com/embed.js"]')[0].onload=hookDUOSHUO_tp;
            function hookDUOSHUO_tp(){
                var _D_post=DUOSHUO.templates.post
                DUOSHUO.templates.post=function (e,t){
                    var rs=_D_post(e,t);
                    if(e.post.agent&&/^Mozilla/.test(e.post.agent))rs=rs.replace(/<\/div><p>/,show_ua(e.post.agent)+'</div><p>');
                    return rs;
                }
            }
            function show_ua(string){
                console.log(string)
                $.ua.set(string);
                var sua=$.ua;
                if(sua.os.version=='x86_64')sua.os.version='x64';
                return '<span class="this_ua platform '+sua.os.name+'">'+sua.os.name+' '+sua.os.version+'</span><span class="this_ua browser '+sua.browser.name+'">'+sua.browser.name+'|'+sua.browser.version+'</span>';
            }
</script>
```

## jquery

这个不用说了吧！下载1.X的都行。

## ua-parser-js

判断UA的jquery的插件

[ua-parser-js][2]


## 合并后的js

```js
<script type="text/javascript">
            var duoshuoQuery = {short_name:"phpgao"};
            (function() {
                var ds = document.createElement('script');
                ds.type = 'text/javascript';ds.async = true;
                ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
                ds.charset = 'UTF-8';
                (document.getElementsByTagName('head')[0]
                || document.getElementsByTagName('body')[0]).appendChild(ds);
            })();

            if (typeof DUOSHUO !== 'undefined')hookDUOSHUO_tp();
            else $('[src="http://static.duoshuo.com/embed.js"]')[0].onload=hookDUOSHUO_tp;
            function hookDUOSHUO_tp(){
                var _D_post=DUOSHUO.templates.post
                DUOSHUO.templates.post=function (e,t){
                    var rs=_D_post(e,t);
                    if(e.post.agent&&/^Mozilla/.test(e.post.agent))rs=rs.replace(/<\/div><p>/,show_ua(e.post.agent)+'</div><p>');
                    return rs;
                }
            }
            function show_ua(string){
                console.log(string)
                $.ua.set(string);
                var sua=$.ua;
                if(sua.os.version=='x86_64')sua.os.version='x64';
                return '<span class="this_ua platform '+sua.os.name+'">'+sua.os.name+' '+sua.os.version+'</span><span class="this_ua browser '+sua.browser.name+'">'+sua.browser.name+'|'+sua.browser.version+'</span>';
            }
</script>

<div class="ds-thread" data-thread-key="<?php echo $this->cid;?>" data-title="<?php echo $this->title;?>" data-author-key="<?php echo $this->authorId;?>" data-url=""></div>

```

## 最后的操作

将CSS、js引入头部`header.php`

再将多说的js引入`comments.php`

完！

  [1]: http://git.oschina.net/huhuhuhu/Make-DUOSHUO-Show-UA
  [2]: https://github.com/faisalman/ua-parser-js/tree/master/src