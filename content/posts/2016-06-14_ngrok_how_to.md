---
title: "自己建立ngrok服务器进行内网穿透"
categories: [ "服务器技术" ]
tags: [ "ngrok" ]
draft: false
slug: "ngrok_how_to"
date: "2016-06-14 11:31:00"
url: "ngrok_how_to.html"
---

随着国内运营商的IP不够用的节奏，我们拨号拿到的IP已经不再是类似`122.1.33.22`的公网IP了，而是类似`10.1.2.33`的内网IP，换句话说，我们没法直接访问到我们自己家里的路由器了！如何解决这个问题呢？目前成本最低的方式就是使用内网穿透技术了。说白了就是端口转发，即我们需要一台类似反向代理的VPS([老高的VPS使用的搬瓦工][1])，把我们的请求转发到内网里，这样就相当于在防火墙内打了个洞，我们的数据能够穿透运营商的限制到达内网设备。

![ngrok原理][2]

图片来自网络，侵删。


<!--more-->



关于内网穿透，老高以前写过用ssh做这件事情，[使用ssh的反向隧道管理内网路由器][3]，这次我们使用ngrok开源出来的代码实现。

## 准备

### GO语言

我们的机器需要先安装GOLANG开发环境，系统不限，因为GO语言是跨平台的！安装过程很简单，参考[官网的教程][4]即可！

### 克隆代码

然后我们需要把代码拷贝至本机，使用`git`命令即可！

```bash
git clone https://github.com/inconshreveable/ngrok.git
cd ngrok
```

### 解析域名

因为我们使用了自己的域名(以`ngrok.phpgao.com`为例)，我们需要做以下解析：

```
ngrok.phpgao.com     -------> A记录到你的VPS IP
*.ngrok.phpgao.com   ------->  CNAME到ngrok.phpgao.com
```

### 生成证书

因为我们使用了自己的域名(以`ngrok.phpgao.com`为例)，所以我们需要自己签名的证书。

```
export NGROK_DOMAIN="grok.phpgao.com"

openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=$NGROK_DOMAIN" -days 36500 -out rootCA.pem
openssl genrsa -out device.key 2048
openssl req -new -key device.key -subj "/CN=$NGROK_DOMAIN" -out device.csr
openssl x509 -req -in device.csr -days 36500 -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt 
cp rootCA.pem assets/client/tls/ngrokroot.crt
cp device.crt assets/server/tls/snakeoil.crt 
cp device.key assets/server/tls/snakeoil.key
```

## 编译运行软件

接下来就是愉快的编译时间，需要注意的是，老高在Linux的编译出来的程序默认只能跑在Linux机器上，如果需要编译出能够在其他系统上跑的程序，我们需要一个编译技巧！-- 就是在编译命令前修改环境变量，例如这样`GOOS=darwin GOARCH=amd64 make release-all`，编译后我们在bin文件夹下能找到编译好的文件，如果是跨平台编译就在`bin/darwin_amd64`文件夹里找。

### 服务端

```bash
# 服务端运行在Linux下，我们不需要任何操作
# 发布server版
make release-server
# make release-xxx会把asset文件里面的东西打包成一个可执行文件

# 运行服务端
# 帮助信息
./ngrokd -h
Usage of ./ngrokd:
  -domain string
    	Domain where the tunnels are hosted (default "ngrok.com")
  -httpAddr string
    	Public address for HTTP connections, empty string to disable (default ":80")
  -httpsAddr string
    	Public address listening for HTTPS connections, emptry string to disable (default ":443")
  -log string
    	Write log messages to this file. 'stdout' and 'none' have special meanings (default "stdout")
  -log-level string
    	The level of messages to log. One of: DEBUG, INFO, WARNING, ERROR (default "DEBUG")
  -tlsCrt string
    	Path to a TLS certificate file
  -tlsKey string
    	Path to a TLS key file
  -tunnelAddr string
    	Public address listening for ngrok client (default ":4443")

./ngrokd -domain=$NGROK_DOMAIN
# 可以指定端口
./ngrokd -domain=$NGROK_DOMAIN -httpAddr=":8080" -httpsAddr=":8081"
```

运行成功后的截图：

![ngrokd][5]


### 客户端

客户端要做的事情就是把本机的端口暴露给VPS，这样VPS就能够转发请求了

```
# 假设我要在mac上运行客户端，需要在编译命令前加上一些参数
GOOS=darwin GOARCH=amd64 make release-client
# 编译好后scp到本地
scp xxx xxx


# 下面开始本地配置

# 打开配置文件
vim ngrok.cfg

# 添加一下两行
# 第一行是将要绑定的域名+4443端口

server_addr: "ngrok.phpgao.com:4443"
trust_host_root_certs: true

# 帮助信息
./ngrok -h
Examples:
	ngrok 80
	ngrok -subdomain=example 8080
	ngrok -proto=tcp 22
	ngrok -hostname="example.com" -httpauth="user:password" 10.0.0.1

# 8080就是我们要转发的端口了
./ngrok -config=./ngrok.cfg 8080

# 指定协议和端口，不指定默认是 http+https
./ngrok -config=./ngrok.cfg -proto=tcp 22

# 指定子域名，不指定就会随机生成
./ngrok -config=./ngrok.cfg -subdomain=test 8080
```

![ngrok][6]

在浏览器中输入`http://127.0.0.1:4040`，就可以看到请求的具体信息了！是不是很神奇！

![http][7]


看到这里是不是还没有成功？不用怕，国内雷锋还是有很多的，不信你看！

[又一个国内的 Ngrok 服务器分享][8]
[NATAPP][9]
[Ngrok国内免费服务器——糖果科技][10]
[Sunny-Ngrok内网转发][11]

## 开机启动

```bash
vim /etc/rc.local

# 添加以下命令，注意使用绝对路径
# 服务端
nohup /path/to/ngrokd -domain=ngrok.phpgao.com -log="/tmp/ngrok.log" >/dev/null 2>&1 &

# 客户端
nohup ./ngrok -config ngrok.cfg -log stdout -log-level="INFO" -subdomain=test 8080 >/tmp/ngrok.log 2>&1 &
```


## 补充

关于配置文件，我们可以使用yml(YAMLAintMarkupLanguage)格式指定更多的信息

```yml
tunnels:
  webapp:
    proto:
      http: 8080
    auth: "user:pw"
  ssh:
    proto:
      tcp: 22
```

然后我们可以使用以下命令执行客户端了！

```bash
# 运行某些
ngrok -congfig=ngrok.cfg start ssh
ngrok -congfig=ngrok.cfg start ssh webapp
# 运行全部
ngrok -congfig=ngrok.cfg start-all
```

Reference:

[inconshreveable/ngrok][12]
[Docs][13]
[Run Ngrok on Your Own Server Using Self-Signed SSL Certificate][14]
[自行编译ngrok服务端客户端，替代花生壳，跨平台][15]


  [1]: https://blog.phpgao.com/vps.html
  [2]: https://blog.phpgao.com/usr/uploads/2016/06/3882816681.png
  [3]: https://blog.phpgao.com/ssh-reverse-tunnel.html
  [4]: https://golang.org/dl/
  [5]: https://blog.phpgao.com/usr/uploads/2016/06/3062355758.png
  [6]: https://blog.phpgao.com/usr/uploads/2016/06/1299567371.png
  [7]: https://blog.phpgao.com/usr/uploads/2016/06/86833163.png
  [8]: http://v2ex.com/t/233759#reply0
  [9]: https://natapp.cn
  [10]: http://qydev.com
  [11]: http://www.ngrok.cc
  [12]: https://github.com/inconshreveable/ngrok/blob/master/docs/DEVELOPMENT.md
  [13]: https://ngrok.com/docs
  [14]: http://www.svenbit.com/2014/09/run-ngrok-on-your-own-server/
  [15]: http://www.ekan001.com/articles/38