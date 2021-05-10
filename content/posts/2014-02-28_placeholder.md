---
title: "让你的IE也支持placeholder属性"
categories: [ "代码人生" ]
tags: [ "IE","placeholder" ]
draft: false
slug: "placeholder"
date: "2014-02-28 13:32:00"
url: "placeholder.html"
---

placeholder属性是html5新加入的，所以非现代浏览器当然不会支持了。 已有牛人写了一下jquery插件实现了placeholder 

## 项目主页

<a href="https://github.com/mathiasbynens/jquery-placeholder" target="_blank">https://github.com/mathiasbynens/jquery-placeholder</a> 

## 使用方法

### html

    <input type="text" name="name" placeholder="e.g. John Doe">
    <input type="email" name="email" placeholder="e.g. address@example.ext">
    <input type="url" name="url" placeholder="e.g. http://mathiasbynens.be/">
    <input type="tel" name="tel" placeholder="e.g. +32 472 77 69 88">
    <input type="password" name="password" placeholder="e.g. h4x0rpr00fz">
    <input type="search" name="search" placeholder="Search this site…">
    <textarea name="message" placeholder="Your message goes here"></textarea>

### js这样写： 

    $('input, textarea').placeholder();

### css提示

这个插件会自动添加一个名为class="placeholder" ，如果想自己区分来，那就使用下面的css定义 

    input, textarea { color: #000; }
    .placeholder { color: #aaa; }

动手试试吧！