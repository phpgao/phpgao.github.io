---
title: "批量复制ssh密钥到Linux系统的主机"
categories: [ "服务器技术" ]
tags: [ "ssh","brew" ]
draft: false
slug: "batch_key_copy"
date: "2019-12-20 07:12:00"
url: "batch_key_copy.html"
---

老高的测试环境中机器很多，每次登录都要输入密码很烦人，下面的脚本可以批量导入公钥到指定多个ip中，使用后记得把文件删除掉！

> sshpass在macos下可能不好搞定，brew因为安全问题默认不给安装，可以通过别人写好的脚本来安装，其他系统自己想办法！很简单。
> `brew install https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb`

<!--more-->


```bash
TMP_PASS=yourpassword
while read SERVER
do
    echo ${SERVER}
    sshpass -p $TMP_PASS ssh-copy-id root@"${SERVER}"
done <<\EOF
1.1.1.1
2.2.2.2
EOF
```