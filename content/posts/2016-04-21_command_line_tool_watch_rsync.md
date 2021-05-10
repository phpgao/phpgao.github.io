---
title: "两个好用的命令行工具-watch-和-rsync"
categories: [ "服务器技术" ]
tags: [ "command" ]
draft: false
slug: "command_line_tool_watch_rsync"
date: "2016-04-21 08:16:00"
url: "command_line_tool_watch_rsync.html"
---

几句话说完。

watch有监控的需求，比如文件列表的变化等。

```bash
# -5 指的是间隔5s执行一次
# --differences 指高亮变化的地方
watch -n 5 --differences ls -l
```

rsync不多说了，主要解决了我在服务器之间的大文件同步需求，scp本来也能做，但是如果文件名相同会直接覆盖。

```bash
# --update 不覆盖更新的文件
# --times 选项保持每个文件的访问权限、创建和最后修改时间
rsync -e ssh --update --verbose --times someone@someip:~/* /tmp/
```


