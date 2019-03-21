<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2018-12-28
title: Supserset 安装配置
tags: superset
category: Supserset
status: publist
summary: Supserset 安装配置
-->

# 开始
---
## 解压包 & 配置目录结构 ##
```
cd /data/
mkdir deepleaper
mv seafile-server_* deepleaper
cd deepleaper
#将 seafile-server_* 移动到 haiwen 目录下后
tar -xzf seafile-server_*
mkdir installed
mv seafile-server_* installed
```

## Seafile 安装配置 ##

```
# on CentOS 7
yum -y install epel-release
rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
yum -y install python-imaging MySQL-python python-memcached python-ldap python-urllib3 ffmpeg ffmpeg-devel
pip install pillow moviepy


cd seafile-server-6.3.3
./setup-seafile-mysql.sh



run seafile server:     ./seafile.sh { start | stop | restart }
run seahub  server:     ./seahub.sh  { start <port> | stop | restart <port> }
```

## 开启 Office/PDF 文件在线预览

sudo yum install libreoffice libreoffice-headless libreoffice-pyuno poppler-utils

### 开启配置项
打开 conf/seafevents.conf, 添加:
```
[OFFICE CONVERTER]
enabled = true
```
保存 seafevents.conf 后，重启 Seafile 服务 ./seafile.sh restart

[Seafile官方文档][100]
[开启 Office/PDF 文件在线预览][101]

[100]:https://manual-cn.seafile.com/deploy/using_mysql.html
[101]:https://manual-cn.seafile.com/deploy_pro/office_documents_preview.html
