---
title: "真•BUG检查方法"
categories: [ "代码人生" ]
tags: [ "bug","deal" ]
draft: false
slug: "way_to_find_bugs"
date: "2014-12-23 07:44:00"
url: "way_to_find_bugs.html"
---

以下是老高总结的BUG发现点，每一点都可以神展开

欢迎大家补充


1. 文件路径是否不一致
1. 操作系统的不一致
1. 数据源是否不一致(数据库配置)
1. 单词是否拼错(很常见)
1. 检查hosts文件
1. 网络是否联通(网线插了没?)
1. 防火墙设置问题
1. 硬盘空间是否已满(df -h)
1. 端口设置是否不一致
1. 域名是否解析正确
1. 编码是否错误
1. 关键字是否被屏蔽(发短信如果带'测试'二字很可能被短信运营商屏蔽)
1. PHP的BOM(幽灵bug，一般与二进制文件有关，比如输出图片)
1. 服务器配置是否不一致
1. 硬件是否出现错误
1. 数据库索引是否出错(有幸遇见过一次,数据存在，where就是查不出，重建索引解决)

以上




附一些有趣的BUG：

http://www.zhihu.com/question/21747929

http://www.zhihu.com/question/21991014

http://www.zhihu.com/question/19955793

http://www.zhihu.com/question/20212145