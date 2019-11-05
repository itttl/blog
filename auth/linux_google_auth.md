<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2017-03-20
title: SSH 添加Google安全验证模块(动态密码验证)
tags: Authenticator,Google,SSH,安全验证模块
category: Authenticator
status: publist
summary: Google,auth,安全验证模块,动态密码验证
-->

![gitblog-logo](../img/logo_64x64.png)


### 安装依赖
```
yum install gcc gcc-c++ make 
yum install pam-devel make gcc-c++ wget
```
### 下载
```
wget https://google-authenticator.googlecode.com/files/libpam-google-authenticator-1.0-source.tar.bz2
tar jxvf libpam-google-authenticator-1.0-source.tar.bz2 
```
### 安装pam模块
```
cd libpam-google-authenticator-1.0 
make
make install
```
### 生成公钥
```
google-authenticator #生成公钥
```
### 启用Google验证模块
```
vim /etc/pam.d/sshd 
---------------------
auth       required pam_google_authenticator.so   #加载第一验证模块之前
---------------------

vim /etc/ssh/sshd_config 
---------------------
ChallengeResponseAuthentication yes # 修改
---------------------
```

service sshd restart #重启后切勿直接关闭终端


### 证书验证信息 && 万能秘钥
```
cat ~/.google_authenticator

CJEWH6THAJH5FUKI
" RESETTING_TIME_SKEW 47822956-958 47823967-958 47823452-959
" RATE_LIMIT 3 30 1439976483 1439976496
" DISALLOW_REUSE 47959316
" TOTP_AUTH
52449723
11133056
68918952
53756951
95063501
```
