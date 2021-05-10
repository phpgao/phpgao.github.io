---
title: "证书，密钥，加密，rsa到底是啥？"
categories: [ "代码人生" ]
tags: [ "rsa","ca" ]
draft: false
slug: "encryption_decryption"
date: "2016-06-03 06:27:00"
url: "encryption_decryption.html"
---

简单的回答：

证书也叫CA(Certification Authority)证书；密钥就是用来加解密用的文件或者字符串；rsa即非对称加密算法。

![RSA][1]


<!--more-->

## 密钥

密钥在非对称加密的领域里，指的是私钥和公钥，他们总是成对出现，其主要作用是加密和解密，具体原理可以参考[RSA加密算法][2]。

需要指出的是，WIKI中提到**1999年，RSA-155 (512 bits)被成功分解**，**2009年12月12日，编号为RSA-768（768 bits, 232 digits）数也被成功分解**，目前老高建议直接使用2048-bit或4086-bit的加密强度。

### openssl

- [openssl EN文档][3]
- [openssl 中文文档][4]

在说私钥公钥之前，大家可能需要先熟悉熟悉openssl命令行工具，[openssl][5]是一个强大的加密、解密工具，由开源组织维护。利用openssl工具，我们可以实现一些常见的摘要算法，如[Linux下简单计算md5,base64,sha1,sha2][6]

```bash
# 帮助信息

Standard(标准) commands
asn1parse      ca             ciphers        crl            crl2pkcs7
dgst           dh             dhparam        dsa            dsaparam
ec             ecparam        enc            engine         errstr
gendh          gendsa         genrsa         nseq           ocsp
passwd         pkcs12         pkcs7          pkcs8          prime
rand           req            rsa            rsautl         s_client
s_server       s_time         sess_id        smime          speed
spkac          verify         version        x509

Message Digest(信息摘要) commands (see the `dgst' command for more details)
md2            md4            md5            mdc2           rmd160
sha            sha1

Cipher(加密) commands (see the `enc' command for more details)
aes-128-cbc    aes-128-ecb    aes-192-cbc    aes-192-ecb    aes-256-cbc
aes-256-ecb    base64         bf             bf-cbc         bf-cfb
bf-ecb         bf-ofb         cast           cast-cbc       cast5-cbc
cast5-cfb      cast5-ecb      cast5-ofb      des            des-cbc
des-cfb        des-ecb        des-ede        des-ede-cbc    des-ede-cfb
des-ede-ofb    des-ede3       des-ede3-cbc   des-ede3-cfb   des-ede3-ofb
des-ofb        des3           desx           rc2            rc2-40-cbc
rc2-64-cbc     rc2-cbc        rc2-cfb        rc2-ecb        rc2-ofb
rc4            rc4-40         seed           seed-cbc       seed-cfb
seed-ecb       seed-ofb

# 加密的相关命令
options are
-in <file>     input file
-out <file>    output file
-pass <arg>    pass phrase source
-e             encrypt
-d             decrypt
-a/-base64     base64 encode/decode, depending on encryption flag
-k             passphrase is the next argument
-kfile         passphrase is the first line of the file argument
-md            the next argument is the md to use to create a key
                 from a passphrase.  One of md2, md5, sha or sha1
-K/-iv         key/iv in hex is the next argument
-[pP]          print the iv/key (then exit if -P)
-bufsize <n>   buffer size
-engine e      use engine e, possibly a hardware device.
```

### 处理数据

#### 信息摘要

```bash
# base64
echo -n "phpgao" | openssl base64
# 解密 -d == decrypt
echo "cGhwZ2Fv" | openssl base64 -d
# 对一个文件计算摘要，下同
openssl base64 -in nginx.conf
# md5
echo -n "phpgao" | openssl md5
# sha128
echo -n "phpgao" | openssl sha1
```

#### 加密解密

```bash
# 使用rc4算法加密php字符串，使用密钥phpgao，输出使用base64编码
echo -n "php" | openssl rc4 -k phpgao -base64

# 使用rc4算法解密字符串，使用密钥phpgao，输入使用base64编码
echo U2FsdGVkX18f3qEoEhVf+hsNOg== | openssl rc4 -d -k phpgao -base64
```

#### 密钥相关操作

```bash
# 生成私钥
# genrsa 指使用rsa算法生成密钥文件
# -des3 指的是给私钥加密的算法(可选)
openssl genrsa -des3 -out key_rsa 4096
openssl genrsa -out key_rsa 4096
# 移除已经存在的密码(先做一下备份)
cp key_rsa old.key
openssl rsa -in old.key -out key_rsa

# 根据刚才创建的私钥创建公钥
openssl rsa -in key_rsa -pubout -out key_rsa.pub

# 使用公钥加密数据
# -in 输入文件
echo -n "phpgao" | openssl rsautl -encrypt -pubin -inkey key_rsa.pub -out a.txt

# 使用私钥解密数据
openssl rsautl -decrypt -inkey key_rsa -in a.txt

# 查看密钥信息
openssl rsa -in key_rsa -text -noout
openssl rsa -pubin -in key_rsa.pub -text -noout

# 检查私钥
openssl rsa -check -in key_rsa
# 检查一个CSR文件(后面有讲)
openssl req -text -noout -verify -in domain.csr
# 检查证书信息
openssl x509 -text -noout -in domain.crt
```

#### 一些补充

##### 关于密钥的格式

密钥的格式有很多种，我们在使用的时候需要注意。我们刚才使用openssl生成的密钥格式叫PKCS#1，不同的程序可能需要不同格式的密钥，不同格式的密钥是可以转换的。

RSA Private Key ([PKCS#1][7]):

```
-----BEGIN RSA PRIVATE KEY-----
BASE64 ENCODED DATA
-----END RSA PRIVATE KEY-----
```

PKCS#1 RSA Public Key:

```
-----BEGIN RSA PUBLIC KEY-----
BASE64 ENCODED DATA
-----END RSA PUBLIC KEY-----
```

X.509 Public Key:

```
-----BEGIN PUBLIC KEY-----
BASE64 ENCODED DATA
-----END PUBLIC KEY-----
```

PKCS#8 Private Key:

```
-----BEGIN PRIVATE KEY-----
BASE64 ENCODED DATA
-----END PRIVATE KEY-----
```

PKCS#8 Encrypted Private Key:

```
-----BEGIN ENCRYPTED PRIVATE KEY-----
BASE64 ENCODED DATA
-----END ENCRYPTED PRIVATE KEY-----
```

SSH public key:

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC6Fo/HHPELU4TLdQwZtidB4hph8xTQberQ8467l2wkXFKV1dlBncCLUA6SUD9JypHIb3I6VNv3rnoEWtRsQYFX5TwGqnxxxH7ZjMt2O1NE9OTEf7H50QmkhHBEKruuEt+OrRXSL6gwHwuAYvGl2nWyKhdaKPg2AMHV6NV3hKEiyEhCe9XCg72KTdHTOVpDS2RjnsU4spyioi9OVj5Eu3vjBeQUifULJzbgMqB0kYS+mJSiol+ZK2YOp1HdiR7vxqfi7r+d8LDhGox8mO00nlYc4h7LBOe75G5KktSbTt5Pyiim+DF6efvmtj2zJ/nN3H2ZHG1dLVQl82QJ8Kz3Rosh laogao@local
```

参考[Converting OpenSSH public keys][8]，我们可以使用命令 `ssh-keygen -f id_rsa.pub -e -m pem` 将ssh-rsa格式转为PKCS#1 PEM格式。



更多格式（我们经常用到的没几个）：

```C
#define PEM_STRING_X509_OLD "X509 CERTIFICATE"
#define PEM_STRING_X509     "CERTIFICATE"
#define PEM_STRING_X509_PAIR    "CERTIFICATE PAIR"
#define PEM_STRING_X509_TRUSTED "TRUSTED CERTIFICATE"
#define PEM_STRING_X509_REQ_OLD "NEW CERTIFICATE REQUEST"
#define PEM_STRING_X509_REQ "CERTIFICATE REQUEST"
#define PEM_STRING_X509_CRL "X509 CRL"
#define PEM_STRING_EVP_PKEY "ANY PRIVATE KEY"
#define PEM_STRING_PUBLIC   "PUBLIC KEY"
#define PEM_STRING_RSA      "RSA PRIVATE KEY"
#define PEM_STRING_RSA_PUBLIC   "RSA PUBLIC KEY"
#define PEM_STRING_DSA      "DSA PRIVATE KEY"
#define PEM_STRING_DSA_PUBLIC   "DSA PUBLIC KEY"
#define PEM_STRING_PKCS7    "PKCS7"
#define PEM_STRING_PKCS7_SIGNED "PKCS #7 SIGNED DATA"
#define PEM_STRING_PKCS8    "ENCRYPTED PRIVATE KEY"
#define PEM_STRING_PKCS8INF "PRIVATE KEY"
#define PEM_STRING_DHPARAMS "DH PARAMETERS"
#define PEM_STRING_DHXPARAMS    "X9.42 DH PARAMETERS"
#define PEM_STRING_SSL_SESSION  "SSL SESSION PARAMETERS"
#define PEM_STRING_DSAPARAMS    "DSA PARAMETERS"
#define PEM_STRING_ECDSA_PUBLIC "ECDSA PUBLIC KEY"
#define PEM_STRING_ECPARAMETERS "EC PARAMETERS"
#define PEM_STRING_ECPRIVATEKEY "EC PRIVATE KEY"
#define PEM_STRING_PARAMETERS   "PARAMETERS"
#define PEM_STRING_CMS      "CMS"
```

列表来自[what is the differences between “BEGIN RSA PRIVATE KEY” and “BEGIN PRIVATE KEY”][9]

## CA证书

证书与密钥不同，CA证书指的是权威机构给我们颁发的证书，我们的电脑或者电子设备中内置了根证书，我们的电脑信任根证书。

```

   +-----------------+                    +--------------------+
   |                 |                    |                    |
   |                 |                    |                    |
   |                 |                    |                    |
   |   browser/PC    |  ① GET / HTTP/1.0 |                    |
   |                 |-------------------->  sites over https  |
   |                 ① WAIT! Where is CA ?|                   |
   |                 +-------------------->                    |
   |                 |  ④ Anyway,give me |                    |
   +-----+----^------+   the code!        +--------------------+
         |    |
         |    |
         |    |③ He is good!
② Is's it|    |
a bad site?   |
         |    |    +----------------------+
         |    |    |                      |
         |    +----+                      |
         |         |                      |
         |         |         CA           |
         +--------->                      |
                   |                      |
                   |                      |
                   +----------------------+

```

### 证书原理

CA证书能够帮助我们鉴别某一个网站的合法性，简单的概括一下原理：

1. 生成我的公钥
1. CA机构用自己的私钥加密我的公钥以及相关信息
1. 客户信任CA，并拥有CA的公钥，客户就可以使用公钥解密加密后的证书，并从证书中得到我的公钥
1. 如果能用CA的公钥解密出数据，说明我的证书是经过CA认证过的，客户就可以放心访问了，如果系统发现证书不是权威CA机构颁发的，会警告用户
1. 客户使用我的公钥解密数据，然后进行信息交换

有一些需要注意的地方：

- 证书有有效期这一说
- 证书中的相关信息，需要包括改证书到根证书的信任链，如下图，bing.com的证书中，就能够顺藤摸瓜，找到根CA机构，所以在生成自己的证书后，一定要加入这个信任链才能让系统正确的识别到与证书相关的最终根CA。

![bing的证书信息][10]

**↑** bing的证书信息

![blog.phpgao.com的证书][11]

**↑** blog.phpgao.com的证书信息

### 证书格式

SSL使用X.509证书标准，编码格式有两种。

#### 证书的编码格式

PEM(Privacy Enhanced Mail)，通常用于数字证书认证机构（Certificate Authorities，CA），扩展名为.pem, .crt, .cer, and .key。内容为Base64编码的ASCII码文件，有类似"-----BEGIN CERTIFICATE-----" 和 "-----END CERTIFICATE-----"的头尾标记。服务器认证证书，中级认证证书和私钥都可以储存为PEM格式（认证证书其实就是公钥）。Apache和nginx等类似的服务器使用PEM格式证书。

DER(Distinguished Encoding Rules)，与PEM不同之处在于其使用二进制而不是Base64编码的ASCII。扩展名为.der，但也经常使用.cer用作扩展名，所有类型的认证证书和私钥都可以存储为DER格式。Java使其典型使用平台。

#### 证书编码的转换

```
# PEM转为DER
openssl x509 -in cert.crt -outform der -out cert.der

# DER转为PEM
openssl x509 -in cert.crt -inform der -outform pem -out cert.pem
```

### CSR

> In public key infrastructure (PKI) systems, a certificate signing request (also CSR or certification request) is a message sent from an applicant to a certificate authority in order to apply for a digital identity certificate. The most common format for CSRs is the PKCS #10 specification and another is the Signed Public Key and Challenge Spkac format generated by some Web browsers.  - [Wiki of Certificate signing request][12]

CSR(Certificate Signing Request)，它是向CA机构申请数字身份证书时使用的请求文件，他最常见的格式是PKCS #10。在生成请求文件前，我们需要准备一对对称密钥。私钥信息自己保存，请求中会附上公钥信息以及国家，城市，域名，Email等信息，csr中还会附上签名信息。当我们准备好CSR文件后就可以提交给CA机构，等待他们给我们签名，签好名后我们会收到crt文件，即证书。


```
# 帮助
openssl req -h
# 生成CSR请求文件和私钥
openssl req -nodes -days 365 -newkey rsa:2048 -keyout key_rsa -out domain.csr
# 从已存在的私钥生生成CSR请求文件
openssl req -new -days 365 -key key_rsa -out domain.csr

# 信息可以直接写好
openssl req -new -days 3650 -key key_rsa -out domain.csr -subj \
"/C=CN/ST=Shanghai/L=Shanghai/O=Qihoo 360/OU=IT Dept/CN=blog.phpgao.com/emailAddress=admin@phpgao.com"

# 检查CSR文件信息
openssl req -text -noout -verify -in domain.csr
```

### CRT

其实我们最终就需要一个私钥文件和证书文件，这一步骤我们会得到CRT文件。

#### 自签名

当我们生成好CSR文件后，就可以开始签名了，是的，自己给自己签名！

```
openssl ca -h
# 先生成一个CA根证书
openssl req -x509 -nodes -days 36500 -newkey rsa:2048 -keyout key_ca_rsa -out ca.crt
# 使用CA的私钥签名
openssl ca -policy policy_anything -days 365 -cert ca.crt -keyfile key_rsa -in domain.csr -out domain.crt

# 可能会出错，因为缺少一些必要文件
mkdir -p demoCA/newCerts
touch demoCA/index.txt 
echo -n "01" > demoCA/serial

```

#### 直接生成证书

我们当然也可以直接生成CRT文件。

```
# 生成证书和私钥
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout key_rsa -out key.crt
# 从已存在的私钥生成生成证书
openssl req -x509 -new -days 365 -key domain.key -out domain.crt


# 检查证书信息
openssl x509 -text -noout -in domain.crt
```

## 补充信息

### CSR所需要的信息

```
CN = Country Name (2 letter code)
ST = State or Province Name (full name)
L  = Locality Name (eg, city)
O  = Organization Name (eg, company)
OU = Organizational Unit Name
CN = Common Name (eg, your name or your server's hostname)

# 例子

Country Name (2 letter code) [AU]:CN
State or Province Name (full name) [Some-State]:Shanghai
Locality Name (eg, city) []:Shanghai
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Qihoo 360
Organizational Unit Name (eg, section) []:IT Dept
Common Name (e.g. server FQDN or YOUR name) []:blog.phpgao.com
Email Address []:admin@phpgao.com

# 我们可以在需要输入以上信息的时候直接用下面的选项，就不用在一个一个输入了
-subj \
"/C=CN/ST=Shanghai/L=Shanghai/O=Qihoo 360/OU=IT Dept/CN=blog.phpgao.com/emailAddress=admin@phpgao.com"
```

### Diffie-Hellman

Diffie-Hellman加密算法的最终目的是为了完成通信双方对称秘钥的交互，但是它牛逼在即使处在不安全的环境（有人侦听）也不会造成秘钥泄露，生成的时间会比较长，请耐心等待。

```
openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

使用方法：

```
ssl_dhparam /etc/ssl/certs/dhparam.pem;
```

### SSL的nginx配置

```
# from https://cipherli.st/
# and https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html

ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_prefer_server_ciphers on;
ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
ssl_ecdh_curve secp384r1;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off;
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;

ssl_dhparam /etc/ssl/certs/dhparam.pem;
```


### SSL在线工具

[SSL证书在线工具SSL Online Tools][13]

Reference:

- [那些证书相关的玩意儿(SSL,X.509,PEM,DER,CRT,CER,KEY,CSR,P12等)][14]
- [How To Create a Self-Signed SSL Certificate for Nginx in Ubuntu 16.04][15]
- [OpenSSL Essentials: Working with SSL Certificates, Private Keys and CSRs][16]


  [1]: https://blog.phpgao.com/usr/uploads/2016/06/2098671918.png
  [2]: https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95
  [3]: https://www.openssl.org/docs/manmaster/
  [4]: http://linux.51yip.com/search/openssl
  [5]: http://www.openssl.org
  [6]: https://blog.phpgao.com/md5_sha1_sha2_in_linux.html
  [7]: https://en.wikipedia.org/wiki/PKCS_1
  [8]: http://blog.oddbit.com/2011/05/08/converting-openssh-public-keys/
  [9]: http://stackoverflow.com/questions/20065304/what-is-the-differences-between-begin-rsa-private-key-and-begin-private-key
  [10]: https://blog.phpgao.com/usr/uploads/2016/06/1972470616.png
  [11]: https://blog.phpgao.com/usr/uploads/2016/06/2384445590.png
  [12]: https://en.wikipedia.org/wiki/Certificate_signing_request
  [13]: https://csr.chinassl.net/
  [14]: http://www.cnblogs.com/guogangj/p/4118605.html
  [15]: https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-in-ubuntu-16-04
  [16]: https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs