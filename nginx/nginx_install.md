<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2020-12-16
title: Nginx1.18.0编译安装
tags: nginx,编译安装
category: Nginx
status: publist
summary: Nginx1.18.0编译安装 
-->
```bash
yum install gcc gcc-c++ make -y
cd /opt/ai/pkg
tar zxvf nginx-1.18.0.tar.gz
tar zxvf pcre-8.44.tar.gz
tar zxvf zlib-1.2.8.tar.gz
tar zxvf openssl-1.1.1i.tar.gz
tar zxvf nginx-module-vts-0.1.18.tar.gz

nginx-1.18.0/

./configure --prefix=/opt/ai/nginx \
--with-http_ssl_module \
--with-http_v2_module \
--with-http_realip_module \
--with-pcre=../pcre-8.44 \
--with-zlib=../zlib-1.2.8 \
--with-openssl=../openssl-1.1.1i \
--with-http_stub_status_module \
--with-http_gzip_static_module \
--with-http_flv_module \
--with-stream \
--add-module=../nginx-module-vts-0.1.18 \
--user=ai \
--group=ai

make -j 16
make install
```
