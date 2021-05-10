---
title: "Windows10,Windows7复原音量合成器"
categories: [ "代码人生" ]
tags: [ "windows" ]
draft: false
slug: "win10_sound_mixer_reset"
date: "2017-03-09 14:57:00"
url: "win10_sound_mixer_reset.html"
---

有时候临时降低了某个程序的音量，但是之后如何恢复默认设置确实是个麻烦事儿，

网上搜索了一堆教程有的教程竟然叫你重新安装声卡驱动，简直要死了。。。

![sound_mixer][1]


<!--more-->


将代码复制到电脑的某个地方，另存为reset_sound_mixer.bat，右键管理员执行就将音量合成器重置了。


```bat
@ECHO OFF

ECHO Reset Volume Mixer Settings...

NET STOP Audiosrv
NET STOP AudioEndpointBuilder

REG DELETE "HKCU\Software\Microsoft\Internet Explorer\LowRegistry\Audio\PolicyConfig\PropertyStore" /F
REG ADD "HKCU\Software\Microsoft\Internet Explorer\LowRegistry\Audio\PolicyConfig\PropertyStore"

NET START Audiosrv
```


参考

[How to reset volume mixer levels?][2]


  [1]: https://blog.phpgao.com/usr/uploads/2017/03/2809581131.png
  [2]: https://answers.microsoft.com/en-us/windows/forum/windows_7-pictures/how-to-reset-volume-mixer-levels/39b6e5f2-0ee9-463c-89c9-8257264294cf