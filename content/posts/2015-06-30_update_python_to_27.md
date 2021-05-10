---
title: "快速升级python到2.7"
categories: [ "代码人生" ]
tags: [ "centos","python","pip","easy_install" ]
draft: false
slug: "update_python_to_27"
date: "2015-06-30 05:44:00"
url: "update_python_to_27.html"
---

![python][1]

(python logo来自互联网)

摘自 [Installing python 2.7 on centos 6.3. Follow this sequence exactly for centos machine only][2]


<!--more-->


```bash
yum groupinstall "Development tools" -y
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel -y
cd /tmp
wget --no-check-certificate https://www.python.org/ftp/python/2.7.6/Python-2.7.6.tar.xz
tar xf Python-2.7.6.tar.xz
cd Python-2.7.6
./configure --prefix=/usr/local
make && make altinstall

ln -s /usr/local/bin/python2.7 /usr/local/bin/python

which python


wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py
/usr/local/bin/python2.7 ez_setup.py
/usr/local/bin/easy_install-2.7 pip
which pip
which easy_install
```

删除旧的

```bash
/usr/bin/pip --version
/usr/bin/easy_install --version

cd /usr/local/bin
mv pip pip2.6
mv easy_install easy_install-2.6
ln -s pip2.7 pip
ln -s easy_install-2.7 easy_install


cd /usr/bin/
mv pip pip.old
mv pip2 pip2.old
mv pip2.6 pip2.6.old

mv easy_install easy_install.old
mv easy_install-2.6 easy_install-2.6.old
```

更新PIP

```bash
pip install --upgrade pip
```

新的python都被装到 `/usr/local/bin/` 下。


  [1]: https://blog.phpgao.com/usr/uploads/2015/06/1602052211.png
  [2]: https://github.com/h2oai/h2o-2/wiki/Installing-python-2.7-on-centos-6.3.-Follow-this-sequence-exactly-for-centos-machine-only#how-to-install-python-276-on-centos-63-62-and-64-okay-too-probably-others