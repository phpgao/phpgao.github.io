---
title: "发现一个命令ldd"
categories: [ "服务器技术" ]
tags: [ "Linux","ldd" ]
draft: false
slug: "good_cmd_ldd"
date: "2015-05-17 10:09:00"
url: "good_cmd_ldd.html"
---

ldd是Linux库操作中最实用的一个命令了，他可以查看一个程序所依赖的库。

如果你的程序执行起来缺少某个库，我们就去下载，但是把库放到哪里才能让程序正确是识别出来呢？使用ldd就可以了！

使用方法: `ldd [options] file`
功能:列出file运行所需的共享库
可用选项

```
➜  sbin  ldd --help
Usage: ldd [OPTION]... FILE...
      --help              print this help and exit
      --version           print version information and exit
  -d, --data-relocs       process data relocations  执行符号重部署，并报告缺少的目标对象
  -r, --function-relocs   process data and function relocations   对目标对象和函数执行重新部署，并报告缺少的目标对象和函数
  -u, --unused            print unused direct dependencies  打印不需要链接的库
  -v, --verbose           print all information   打印所有信息
```

例子

```
➜  /bin  ldd /bin/cp
	linux-vdso.so.1 =>  (0x00007fff8dbfe000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f94558e4000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f94556dc000)
	libacl.so.1 => /lib64/libacl.so.1 (0x00007f94554d3000)
	libattr.so.1 => /lib64/libattr.so.1 (0x00007f94552ce000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f9454f3a000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f9454d35000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f9455b0f000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f9454b18000)
```