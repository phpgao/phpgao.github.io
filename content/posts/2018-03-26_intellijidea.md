---
title: "Linux下部署IntelliJIDEALicenseServer"
categories: [ "代码人生" ]
tags: [ "IntelliJ" ]
draft: true
slug: "intellijidea"
date: "2018-03-26 03:10:43"
url: "intellijidea.html"
---

[下载地址][1]

```bash
cat>/etc/systemd/system/intellijidea.service<<EOF
[Unit]
Description=IntelliJIDEA License Server
After=network.target

[Service]
ExecStart=/usr/local/bin/IntelliJIDEALicenseServer_linux_amd64
Restart=always

[Install]
WantedBy=multi-user.target
EOF



systemctl daemon-reload
systemctl enable intellijidea
systemctl restart intellijidea
```


  [1]: http://blog.lanyus.com/category/program/