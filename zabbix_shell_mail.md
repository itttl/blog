<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-05-18
title: Zabbix 邮件报警脚本
tags: zabbix,shell,mail
category: Zabbix
status: publist
summary: Zabbix 邮件报警脚本
-->

![gitblog-logo](./img/logo_64x64.png)


## 直接上脚本
```
#!/bin/bash
#export.UTF-8
FILE=/tmp/mailtmp.txt
echo "$3" >$FILE
dos2unix -k $FILE
/bin/mail -s "$2" $1 < $FILE
```
