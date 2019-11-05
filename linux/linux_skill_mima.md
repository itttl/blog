<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-11-07
title: 【Linux技巧】Linux下产生随机密码方法 
tags: linux,随机16位
category: Linux
status: publish
summary: Linux shell 下产生随机密码方法
-->

![gitblog-logo](./img/logo_64x64.png)

### 有特殊符号的：
```bash
cat /dev/urandom | tr -dc "a-zA-Z0-9_+\~\!\@\#\$\%\^\&\*"| fold -w 16 |head -n 16
```
#### 无特殊符号的：
```bash
#1
date +%s | sha256sum | base64 | head -c 32 ; echo
#2
openssl rand -base64 32
#3
tr -cd '[:alnum:]' < /dev/urandom | fold -w30 | head -n1
#4
dd if=/dev/urandom bs=1 count=32 2>/dev/null | base64 -w 0 | rev | cut -b 2- | rev
#5
date | md5sum
```
- 生成随机16位密码，我一般使用第一个。
