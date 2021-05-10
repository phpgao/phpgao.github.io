---
title: "在linux或MacOS统创建和解压cab(cabinet)文件"
categories: [ "代码人生" ]
tags: [ "Linux","lcab","cabextract","macos" ]
draft: false
slug: "create_and_extract_cab_file_in_linux_or_macos"
date: "2017-08-07 16:22:00"
url: "create_and_extract_cab_file_in_linux_or_macos.html"
---

由于工作原因，需要在Linux下操作cab文件，拜访Google后得到两枚神器，在此与大家分享。


![cab file][1]


<!--more-->


## 解压cab文件cabextract

[cabextract v1.6][2]

### 使用方法

老高偏向使用源代码编译(centos)，当然`yum install cabextract`也是可以解决问题的

```bash
# 如果已有编译环境请忽略
sudo yum groupinstall "development tools" -y

wget https://www.cabextract.org.uk/cabextract-1.6.tar.gz
tar zxvf cabextract-1.6.tar.gz
cd cabextract-1.6
./configure
make
sudo make install


which cabextract
# /usr/local/bin/cabextract

# 查看帮助信息
cabextract --help

# 常用命令

cabextract test.cab
```

## 创建cab文件 lcab

[lcab][3]

```bash
# 由于lcab包的官方下载地址已经无法下载，所以代替为Debian的官方下载地址
wget http://ftp.debian.org/debian/pool/main/l/lcab/lcab_1.0b12.orig.tar.gz

tar zxvf lcab_1.0b12.orig.tar.gz
cd lcab-1.0b12
./configure
make
sudo make install


/usr/local/bin/lcab

which lcab
# /usr/local/bin/lcab

# 查看帮助信息
lcab --help

# 常用命令
lcab -n test.ini test.cab
```


  [1]: https://blog.phpgao.com/usr/uploads/2017/08/3551035709.jpeg
  [2]: https://www.cabextract.org.uk
  [3]: http://freecode.com/projects/lcab