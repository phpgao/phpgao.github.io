---
title: "docker-最新安装命令"
categories: [ "代码人生" ]
tags: [ "centos","docker","note","docker-compose" ]
draft: false
slug: "install_docker_in_centos"
date: "2019-04-26 14:18:00"
url: "install_docker_in_centos.html"
---

docker就不多说了，按照贴出的代码一步一步就可以安装成功


安装之前最好手动关闭selinux

```
setenforce 0
getenforce
# Permissive
sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
```

<!--more-->



```bash
# 删除
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

# 安装依赖
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

# 开启ce
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# 使用阿里的加速
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 安装
sudo yum install -y docker-ce docker-ce-cli containerd.io

# 开启服务
sudo systemctl enable docker
sudo systemctl start docker

# 添加组
sudo groupadd docker
sudo usermod -aG docker ${USER}

# 重新登录
logout
```

安装完docker，最好改为国内镜像，`http://hub-mirror.c.163.com`是docker公司专门为国内用户准备的！

```bash
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors":[
        "http://hub-mirror.c.163.com"
    ],
    "dns":[
        "114.114.114.114",
        "8.8.8.8"
    ]
}
EOF


sudo pkill -SIGHUP dockerd
# 或者
sudo systemctl daemon-reload
sudo systemctl restart docker
```

>还有很多国内加速的服务器可以用，比如

```
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://3laho3y3.mirror.aliyuncs.com",
        "https://hub-mirror.c.163.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://f1361db2.m.daocloud.io",
        "https://registry.docker-cn.com",
        "https://dockerhub.azk8s.cn"
    ]
}
EOF
```


docker-compose就不多说了，赶紧装起来

方法1：

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

方法2：

```
sudo yum install -y epel-release
sudo yum install -y python-pip
pip install -i https://pypi.doubanio.com/simple/ --upgrade pip docker-compose
```

一些常用的加速资源

```bash
docker run -it --rm=true --name=test alpine:3.10 ash

sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/alpine/g' /etc/apk/repositories

npm config set registry https://registry.npm.taobao.org/
composer config -g repo.packagist composer https://packagist.phpcomposer.com
pip install -i https://mirrors.aliyun.com/pypi/simple --upgrade pip docker-compose
pip install -i https://pypi.doubanio.com/simple/ --upgrade pip docker-compose
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
```

k8s

```bash
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
setenforce 0
yum install -y kubelet kubeadm kubectl
systemctl enable kubelet && systemctl start kubelet
```