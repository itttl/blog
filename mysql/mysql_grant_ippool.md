<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-12-28
title: MySQL GRANT一个IP端的授权（MySQL正则）
tags: MySQL,grant
category: MySQL
status: publist
summary: MySQL GRANT一个IP端的授权,MySQL正则 
-->

![gitblog-logo](../img/logo_64x64.png)

## MySQL对用户登录ip段授权的方法

### 172.16.1.0/24
```
GRANT ALL PRIVILEGES ON *.* TO test@'172.16.1.%' identified by 'test123456' 即可。
```
### 172.16.0.0/16
```
GRANT ALL PRIVILEGES ON *.* TO test@'172.16.%' identified by 'test123456' 即可。
```

## MySQL针对某一类（某字段开通的）数据库授权的方法
### bbs_开头的数据库
```
grant all on `bbs_%`.* to test@'172.16.1.3' identified by 'test123456';
```
