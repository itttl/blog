<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-12-26
title: Zabbix Server 安装配置
tags: zabbix,Zabbix Server 
category: Zabbix
status: publist
summary: Zabbix Server 安装配置
-->

![gitblog-logo](./img/logo_64x64.png)

# 一、服务端的安装
## 安装基本依赖包
```bash
yum install mysql-server mysql-devel libcurl-devel net-snmp-devel php php-gd php-xml php-mysql php-mbstring php-bcmath httpd
```
#### 关于php与mysql的安装也可以选择源码编译的.
### 1、下载zabbix包，然后上传到tmp目录，并解压
```bash
tar zxvf zabbix-2.4.6.tar.gz
```
### 2、创建用户
```bash
groupadd zabbix
useradd -g zabbix zabbix -s /sbin/nologin
```
### 3、创建数据库（使用mysql数据库）、用户与导入表
```bash
create database zabbix character set utf8 collate utf8_bin;
grant all on zabbix.* to zabbix@'localhost' identified by 'zabbix';
flush privileges;
cd /usr/local/src/zabbix-2.4.6/database/
mysql -uzabbix -pzabbix  zabbix <mysql/schema.sql
mysql -uzabbix -pzabbix  zabbix <mysql/images.sql
mysql -uzabbix -pzabbix  zabbix <mysql/data.sql
```
### 4、编译安装
```bash
cd /usr/local/src/zabbix-2.4.6/
./configure --prefix=/usr/local/zabbix --enable-server --enable-agent --with-mysql --enable-ipv6 --with-net-snmp --with-libcurl
make install
```
### 5、配置
```bash
cd /usr/local/zabbix/etc/
```
#### 下面是配置
```bash
[root@OMCServer201 ~]# grep -v ^# /soft/zabbix/etc/zabbix_server.conf | grep -v ^$
LogFile=/soft/zabbix/logs/zabbix_server.log
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix***
DBSocket=/home/mysql/log/mysql13306.sock
DBPort=13306
```
### 6 start Zabbix
```bash
/usr/local/zabbix/sbin/zabbix_server
ps -ef|grep zabbix
zabbix   16835     1  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16836 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16837 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16838 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16839 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16840 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16841 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16842 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16843 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16844 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16845 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16846 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16847 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16848 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16849 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16850 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16854 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16855 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16856 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16860 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16861 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16862 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16865 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16866 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16868 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16871 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
zabbix   16873 16835  0 19:34 ?        00:00:00 /usr/local/zabbix/sbin/zabbix_server
root     16883  3771  0 19:34 pts/1    00:00:00 grep zabbix
```
### 7、配置php
```bash
sed -i 's/^\(.*\)date.timezone =.*$/date.timezone = Asia\/Shanghai/g' /etc/php.ini
sed -i 's/^\(.*\)post_max_size =.*$/post_max_size = 16M/g' /etc/php.ini
sed -i 's/^\(.*\)max_execution_time =.*$/max_execution_time = 300/g'  /etc/php.ini
sed -i 's/^\(.*\)max_input_time =.*$/max_input_time = 300/g' /etc/php.ini
```
- 修改完成后别忘记重启php-fpm
### 8、复制web interface到www目录
```bash
mkdir /var/www/html/zabbix
cd /tmp/zabbix-2.0.6/frontends/php/
cp -a . /var/www/html/zabbix/
```
- 确认好web服务器配置并开启，就输入http://ip/zabbix来进行web的安装

