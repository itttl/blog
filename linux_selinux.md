<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-01-04
title: CentOS 永久关闭SELinux
tags: CentOS7 SELinux.SELinux
category: Linux
status: publist
summary: 解决CentOS7永久禁用SELinux不生效问题
-->

![gitblog-logo](./img/logo_64x64.png)

## 一、CentOS5.X 6.X 永久关闭SELinux
```
vim /etc/sysconfig/selinux

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
#SELINUX=enforcing
SELINUX=disabled  ##修改此处为disables
# SELINUXTYPE= can take one of these two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected. 
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```
####重启系统即可

## 二、临时关闭SELinux

```
setenforce 0
```

## 三、CentOS7 永久关闭SELinux

按CentOS5.x 6.x 操作后,重启系统无效。
原因是CentOS7 中CentOS7中文件/etc/sysconfig/selinux并不是/etc/selinux/config的软连接,修改源文件/etc/selinux/config重启后即可生效。
```
vim /etc/selinux/config
#SELINUX=enforcing 
#改为
SELINUX=disabled
```

