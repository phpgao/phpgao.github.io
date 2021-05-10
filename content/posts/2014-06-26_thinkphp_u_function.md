---
title: "在thinphp的模板中使用U方法传递参数"
categories: [ "代码人生" ]
tags: [ "url","thinkphp" ]
draft: false
slug: "thinkphp_u_function"
date: "2014-06-26 03:12:00"
url: "thinkphp_u_function.html"
---

在模板里生成URL如果需要用到模板里的参数时，使用下面代码即可 

    {:U('Index/getInfo',array('userid'=>$userid))}

如果变量有嵌套，那就要用"[]"代替模板中的"." 

    {:U('Index/getInfo',array('userid'=>$user['id']))} 