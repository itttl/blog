<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-11-09
title: CentOS系统tab 指令补齐and参数补齐
tags: CentOS参数补齐
category: Linux
status: publist
summary: CentOS系统tab 指令补齐and参数补齐
-->

![gitblog-logo](./img/logo_64x64.png)

#### 安装bash-completion
```
yum install bash-completion -y
```
#### 添加至环境变量
```
vim /etc/bashrc
#追加如下内容
if [ -f /etc/bash_completion ]; then
        . /etc/bash_completion
fi
```
#### 读取/etc/bash or　退出当前shell重新登陆
```
source /etc/bashrc
```

#### 测试可用性
#kubectl <按两下tab> 可以看到改命令可接的参数  
```
[root@centos7-2 ~]# kubectl 
api-versions    create          exec            label           patch           replace         scale           
cluster-info    delete          expose          logs            port-forward    rolling-update  stop            
config          describe        get             namespace       proxy           run             version
```
