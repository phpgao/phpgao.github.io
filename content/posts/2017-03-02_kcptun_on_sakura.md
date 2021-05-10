---
title: "为樱花打造的KCPTUN"
categories: [ "服务器技术" ]
tags: [ "docker","sakura" ]
draft: false
slug: "kcptun_on_sakura"
date: "2017-03-02 02:51:00"
url: "kcptun_on_sakura.html"
---

老高课余时间研究了一下kcptun，然后维护了一个支持shadowsocks-libv + kcptun的镜像，希望能给大家点帮助。


<!--more-->


链接在此[endoffight/shadowsocks-kcp][1]

shadowsocks版本 3.0.3
kcptun版本 20170303

## 风味1：在樱花使用

结合樱花docker服务，先参考[使用日本樱花arukas的免费docker服务搭建shadowsocks][2]这篇文章搞定注册，然后跟着老高一起创建属于自己的SS服务。

### 服务配置

```dockerfile
ENV SERVER_ADDR 0.0.0.0
ENV SERVER_PORT 3721
ENV PASSWORD    laogao
ENV METHOD      aes-256-cfb
ENV TIMEOUT     300
ENV DNS_ADDR    8.8.8.8
ENV DNS_ADDR_2  8.8.4.4


ENV KCP_LISTEN  3824
ENV KCP_PASS    phpgao
ENV KCP_ENCRYPT aes-192
ENV KCP_MODE    fast
ENV KCP_MUT     1350
```

### 创建APP

点击**Create a new application**按钮创建新的APP

![Create a new application][3]

### 配置

在配置栏先用最小配置测试运行：

![配置][4]

说明：

App Name - 自己随便填

Image - endoffight/shadowsocks-kcp:latest

Memory - 512MB

Port 按照图上配置

其他没有提到的都默认，然后点击保存即可！

### 使用方法

下图是运行后的APP，注意点就是把图中字符XXX-XXX-XXX-XXX 转化成真实IP XXX.XXX.XXX.XXX，比如字符串为123-123-123-123，转换后的IP即123.123.123.123。

![运行截图][5]

从图中我们可以得到以下几个信息：

> 服务器的真实IP
真实端口A -- 对应3271/tcp
真实端口B -- 对应3271/ucp
真实端口C -- 对应3824/ucp

接下来就好办了：

### SS和KCP配置

如果你成功到达此步骤，那么恭喜你，胜利已经向你招手！


#### ss配置

```json
{
	"server" : "真实IP",
	"server_port" : 真实端口A,
	"password" : "laogao",
	"method" : "aes-256-cfb",
	"timeout" : 600
}
```

#### kcptun配置

```
# 此命令让kcptun在1088监听端口
.\client_windows_amd64.exe -r "真实IP:真实端口C" --key phpgao --mode fast --crypt aes-192 -l ":1088"
```

### 自定义配置

如果需要自定义配置的话，请修改对应的ENV环境变量，以修改端口为例：

![自定义配置][6]

此处老高修改了配置中的SERVER_PORT与KCP_LISTEN两个变量，将其改为8080与8081，于是需要映射的端口就需要对应改变了，所以在expose中加入了8080和8081。

如果我需要修改KCPTUN的连接密码，就直接加入env名为KCP_PASS，将其设置为你需要的密码。最后在配置中体现即可！


## 风味2：docker直接运行


```
# 拉取镜像 
docker pull endoffight/shadowsocks-kcp

# 做好端口映射
docker run -p 1080:3721 -p 1081:3824/udp -e "KCP_PASS=nihao" -e "PASSWORD=nichilema" -d endoffight/shadowsocks-kcp

#　完
```

  [1]: https://hub.docker.com/r/endoffight/shadowsocks-kcp/
  [2]: https://blog.phpgao.com/arukas.html
  [3]: https://blog.phpgao.com/usr/uploads/2017/03/78815547.png
  [4]: https://blog.phpgao.com/usr/uploads/2017/03/2150549025.png
  [5]: https://blog.phpgao.com/usr/uploads/2017/03/237341718.png
  [6]: https://blog.phpgao.com/usr/uploads/2017/03/1362728862.png