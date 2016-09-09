<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-12-26
title: Zabbix Agent 安装配置
tags: zabbix,Zabbix agent 
category: Zabbix
status: publist
summary: Zabbix Agent 安装配置
-->

![gitblog-logo](./img/logo_64x64.png)


## 直接上脚本了
```
#!/bin/bash
# author zyl
# fun install zabbix linux client script
# date :20150822
# Email:sdata@foxmail.com
yum install curl-devel gcc gcc-c++ make  -y
hostname=`awk -F= '/HOSTNAME/{print $2}' /etc/sysconfig/network`
zabbix_server="10.10.10.199"
tar -zxvf /opt/zabbix-2.4.6.tar.gz -C /usr/local/src/
useradd zabbix
cd /usr/local/src/zabbix-2.4.6
./configure --prefix=/usr/local/zabbix --enable-agent --with-mysql --with-net-snmp --with-libcurl
make install
mkdir /usr/local/zabbix/logs
chown -R zabbix:zabbix /usr/local/zabbix
sed -i "s#^Server=127.0.0.1#Server=$zabbix_server#g" /usr/local/zabbix/etc/zabbix_agentd.conf
sed -i "s#^ServerActive=127.0.0.1#ServerActive=$zabbix_server#g" /usr/local/zabbix/etc/zabbix_agentd.conf
sed -i "s#^Hostname=Zabbix server#Hostname=$hostname#g" /usr/local/zabbix/etc/zabbix_agentd.conf
sed -i "s#^LogFile=/tmp/zabbix_agentd.log#LogFile=/usr/local/zabbix/logs/zabbix_agentd.log#g" /usr/local/zabbix/etc/zabbix_agentd.conf
/usr/local/zabbix/sbin/zabbix_agentd
grep ^Hostname /usr/local/zabbix/etc/zabbix_agentd.conf
grep ^Server /usr/local/zabbix/etc/zabbix_agentd.conf
ps aux | grep zabbix
echo "/usr/local/zabbix/sbin/zabbix_agentd" >> /etc/rc.local
```
