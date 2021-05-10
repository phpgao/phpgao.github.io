---
title: "一行代码更改密码"
categories: [ "代码人生" ]
tags: [ "passwd" ]
draft: false
slug: "change_passwd_in_one_line"
date: "2016-07-14 08:02:00"
url: "change_passwd_in_one_line.html"
---

修改密码的时候，大家是不是都有输入两次密码的经历，这种交互适合终端操作，而不是用于脚本操作，对于老高这种一言不合就写Dockerfile的猿，真是很不友好。

下面老高就教你怎么使用一行代码更改密码。


<!--more-->


## 方法一：管道

```bash
echo 'root:laogao' | chpasswd

# 此方法使用于需要输入两次密码的情况
(echo 'password'; echo 'password') | pure-pw useradd www -u www-data -d /home/ftpuser/www
```


## 方法二：Python脚本

在此我们使用了[Pexpect][1]模块，我们可以使用`pip install pexpect`安装。然后用一行代码调用即可！

```python
#!/usr/local/bin/python

import pexpect

child = pexpect.spawn('pure-pw useradd www -u www-data -d /home/ftpuser/www');

child.expect('Password: ');
child.sendline('password');

child.expect('Enter it again: ');
child.sendline('password');

child.expect(pexpect.EOF);

print child.before;
```


  [1]: https://pexpect.readthedocs.io/en/stable/