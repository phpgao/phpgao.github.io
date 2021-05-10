---
title: "网站无法选择和复制文字"
categories: [ "代码人生" ]
tags: [ "web","enable_click","copy" ]
draft: false
slug: "rightclick-enable"
date: "2014-03-07 14:55:00"
url: "rightclick-enable.html"
---

有些网站真是可恶，把右键屏蔽了不给复制，这样还不行（因为可以使用快捷键CTRL+C复制） 他们竟然不让我们选择文字。。。这样下来我们既不能复制，连源代码都不能看了。是可忍孰不可忍，既然这样，那我就不客气了！ 

## 找到病根是关键

### 原来是JS捣的鬼 

纯html是无法实现屏蔽右键等功能的，所以很简单就能想到是js的问题，那么我们就找找到底是那些代码段使得我们无法使用邮件功能呢？ 既然右键被禁止了，那我们如何才能看到网页的源代码呢？ 其实很简单，PHP@老高建议你使用chrome浏览器打开你想要破解的网站，使用快捷键CTRL+U即可轻易查看网页的源代码！怎么样，很简单吧？ 好了既然我们已经找到源代码，那就开始找那个害人的代码段吧！ 

### 代码一 这个是比较难找的代码，一般隐藏在网页的头部或者藏在某些引用的js文件中 

    <SCRIPT language=javascript type=text/javascript>
    <!--
    document.oncontextmenu=new Function('event.returnValue=false;');//屏蔽右键
    document.onselectstart=new Function('event.returnValue=false;');//屏蔽选择
    -->
    </SCRIPT>

###  代码二 

这个比较容易找到,直接找到</head>标签，之后就是此句，与代码一异曲同工 

    <body oncontextmenu="self.event.returnValue=false" onselectstart="return false">

##  解决方法

### IE浏览器 使用IE的童鞋可以试试在浏览器的地址栏输入 

    javascript:alert(document.oncontextmenu=document.onselectstart="OK")

或者 

    javascript:alert(document.body.oncontextmenu=document.body.onselectstart="OK")

之后弹出对话框，点击确认即可！现在点击右键或者选择文字试试，是不是已经被破解了？
请注意！该方法可能已经失效，因为新一代浏览器对安全性有了很大的提高，所以在地址栏输入脚本可能会被禁止！


接下来就是将他删除掉即可： 在代码上点击右键：选择第二项Edit Attribute,将其属性删除，结果代码变成这样： 不用怀疑了，刚才限制的功能已经恢复啦！ 

### 其他浏览器

以chrome为例
首先，在被屏蔽的网站上点击F12，出现了如下图所示代码(注：老高@PHPer使用的是chrome v30，不同版本的界面留有不同)

当然，您的火眼金睛可能立即发现了蓝色标记的一行代码

    <body oncontextmenu=”self.event.returnValue=false” onselectstart=”return false”>

接下来就是将他删除掉即可：

在代码上点击右键：选择第二项Edit Attribute,将其属性删除，不用怀疑了，限制的功能已经恢复啦！

## 终极方法 

在浏览器中将以下代码存为书签，名为解锁，在被禁网址中点击一下解锁，即可解开~ 

    javascript:(function() { function R(a){ona = "on"+a; if(window.addEventListener) window.addEventListener(a, function (e) { for(var n=e.originalTarget; n; n=n.parentNode) n[ona]=null; }, true); window[ona]=null; document[ona]=null; if(document.body) document.body[ona]=null; } R("contextmenu"); R("click"); R("mousedown"); R("mouseup"); R("selectstart");})()