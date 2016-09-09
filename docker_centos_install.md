<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-08-09
title:  【docker】CentOS下Docker安装配置
tags: docker,centos,安装配置
category: Docker
status: publist
summary: CentOS7 or CentOS6.X 下Docker安装配置文档
-->

![gitblog-logo](./img/logo_docker.png)

### 一、前提条件 ###
官方文档里有写到，需要使用64为系统并且内核版本高于3.10。　　
这样的要求并不是没有道理。　　
我在CentOS6.5(kernel 2.6.32)运行docker,经常会出现内核崩溃的情况。  
原因可能是因为CPU资源分配不妥当，某些软件版本过低，内核bug。  
所以建议使用CentOS7以上的系统。使用CentOS6.5的话需要升级内核至3.10以上。
如果是云服务器(比如阿里云等),升级内核可能会遇到一些坑。

#### 查看内核版本 ####
```
uname -r
```
### 二、安装 ###
#### 1.yum源安装 ####
```
sudo yum update

cat >/etc/yum.repos.d/docker.repo <<-EOF
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF

yum install docker-engine

service docker start

docker run hello-world
```

#### 2.脚本安装 ###
```
yum update
curl -sSL https://get.docker.com/ | sh
service docker start
docker run hello-world
usermod -aG docker your_username
#测试
docker run hello-world
```
### 三、配置 ###
```
vim /etc/sysconfig/docker
#配置镜像、容器存放位置，私有仓库地址修改如下标签内容
OPTIONS='--graph=/data/docker'
or
OPTIONS='--graph=/data/docker --insecure-registry=10.0.0.10:5000'
```
### 四、启停/查看状态 ###
```
#启动
systemctl start docker.service
#停止
systemctl stop docker.service
#状态
systemctl status docker.service
#开机启动
systemctl enable docker.service
```

### 常用指令
#### 查找镜像
docker search centos
#### 下载镜像
docker pull docker.io/centos
#### 创建容器
docker run -d docker.io/centos /sbin/init
#### 进入容器bash
docker exec -it contiane_id /bin/bash

[Docker官方文档][1]  
[Docker官方文档安装文档-CentOS7][2]  
[Docker实战][3]  

[1]:https://docs.docker.com/
[2]:https://docs.docker.com/engine/installation/centos/
[3]:http://dockerpool.com/
