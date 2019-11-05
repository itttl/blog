<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-11-16
title: Nginx多语言支持(根据请求携带的语言做跳转)
tags: nginx,国际化,多语言
category: Nginx
status: publist
summary: 
-->

### 写在前面 ###
web国际化方式有很多种，从运维角度，可以在Nginx上做一个，根据求情head携带的语言信息做跳转

### Nginx具体配置 ###
```
server {
        listen       80;
        server_name itttl.com;

        location / {
            set $lang zh_CN;

            if ($http_accept_language ~* ^en)       {
                set $lang en_US;
                rewrite (.*) http://en.itttl.com$1 permanent;
                break;
            }

            rewrite (.*) http://www.itttl.com$1 permanent;
        }

        access_log  /data/log/nginx/access/itttl.com.log;
}

```
- 其中en.itttl.com为英文站,www.itttl.com为默认的中文站

### 测试 ###
```
curl -I itttl.com -H "Accept-Language:zh"
HTTP/1.1 301 Moved Permanently
Server: nginx
Date: Tue, 17 Nov 2015 11:10:26 GMT
Content-Type: text/html
Content-Length: 178
Connection: keep-alive
Location: http://www.itttl.com/

$ curl -I itttl.com -H "Accept-Language:en"
HTTP/1.1 301 Moved Permanently
Server: nginx
Date: Tue, 17 Nov 2015 11:10:29 GMT
Content-Type: text/html
Content-Length: 178
Connection: keep-alive
Location: http://en.itttl.com/

```
