---
title: "多说圆形旋转头像"
categories: [ "服务器技术" ]
tags: [ "css","duoshuo" ]
draft: false
slug: "duoshuo_css_custom"
date: "2015-03-08 15:04:00"
url: "duoshuo_css_custom.html"
---

其实现使用了css3的一些特性。使用方法很简单！


<!--more-->


> ps.既然是css3，那么兼容性大家都懂得！

在后台设置中加入自定义css即可！

至于效果如何，留言便知！

```css
#ds-reset .ds-avatar img{   
    width:54px;height:54px; /*设置图像的长和宽，这里要根据自己的评论框情况更改*/  
    border-radius: 27px;/*设置图像圆角效果,在这里我直接设置了超过width/2的像素，即为圆形了*/  
    -webkit-border-radius: 27px;/*圆角效果：兼容webkit浏览器*/  
    -moz-border-radius:27px;   
    box-shadow: inset 0 -1px 0 #3333sf;/*设置图像阴影效果*/  
    -webkit-box-shadow: inset 0 -1px 0 #3333sf;   
    -webkit-transition: 0.4s;      
    -webkit-transition: -webkit-transform 0.4s ease-out;   
    transition: transform 0.4s ease-out;/*变化时间设置为0.4秒(变化动作即为下面的图像旋转360读）*/  
    -moz-transition: -moz-transform 0.4s ease-out;   
}    
#ds-reset .ds-avatar img:hover{/*设置鼠标悬浮在头像时的CSS样式*/  
    box-shadow: 0 0 10px #fff; rgba(255,255,255,.6), inset 0 0 20px rgba(255,255,255,1);   
    -webkit-box-shadow: 0 0 10px #fff; rgba(255,255,255,.6), inset 0 0 20px rgba(255,255,255,1);   
    transform: rotateZ(360deg);/*图像旋转360度*/  
    -webkit-transform: rotateZ(360deg);   
    -moz-transform: rotateZ(360deg);   
}
```


Reference:

http://www.wpdaxue.com/duoshuo-avatar.html