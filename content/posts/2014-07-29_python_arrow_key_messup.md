---
title: "python交互模式下方向键乱码的正确解决方法"
categories: [ "代码人生","服务器技术" ]
tags: [ "python","Linux" ]
draft: false
slug: "python_arrow_key_messup"
date: "2014-07-29 03:10:00"
url: "python_arrow_key_messup.html"
---

# python交互模式下方向键乱码的正确解决方法

今天升级了python后，发现在交互模式中上下左右变成了乱码

    ^[[A ^[[D ^[[B ^[[C ^[[D ^[[D ^[[D ^[[D ^[[D ^[[D ^[[D ^[[D 

搜索了一下，普遍的解决方法是

 > 因为方向键被转义了
原因主要是由于缺少readline Module问题导致的。而CentOS默认只有readline模块而没有readline-devel模块。

 > 解决方法
yum -y install readline-devl
然后重新编译安装python就ok了

再仔细一看，这些问题都是N年前了，yum中的包名早都换了。。。怪不得总是报错，说找不到`readline-devl`，貌似现在开发版后面都变成`devel`了


<!--more-->


下回找不到安装包的时候可以先试试 `yum search package_name`

例子

	yum search readline
	Loaded plugins: fastestmirror
	Loading mirror speeds from cached hostfile
	 * base: centos.tcpdiag.net
	 * extras: centos.tcpdiag.net
	 * updates: mirror-centos.hostingswift.com
	=========================================================================================================== N/S Matched: readline ===========================================================================================================
	compat-readline5-devel.i686 : Files needed to develop programs which use the readline library
	compat-readline5-devel.x86_64 : Files needed to develop programs which use the readline library
	compat-readline5-static.x86_64 : Static libraries for the readline library
	libreadline-java-javadoc.x86_64 : Javadoc for libreadline-java
	mingw32-readline.noarch : MinGW port of readline for editing typed command lines
	----->readline-devel.i686 : Files needed to develop programs which use the readline library
	readline-devel.x86_64 : Files needed to develop programs which use the readline library
	readline-static.x86_64 : Static libraries for the readline library
	compat-readline5.i686 : A library for editing typed command lines
	compat-readline5.x86_64 : A library for editing typed command lines
	libreadline-java.x86_64 : Java wrapper for the EditLine library
	perl-Term-UI.x86_64 : Term::ReadLine UI made easy
	readline.i686 : A library for editing typed command lines
	readline.x86_64 : A library for editing typed command lines

这不一下子就找到了！ :)

PS. 安装完`readline-devel`后不要忘了重新编译python，否则是没有效果的!