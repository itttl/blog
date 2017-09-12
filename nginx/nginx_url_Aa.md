<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2017-08-22
title: Nginx Url忽略大小写匹配
tags: nginx,忽略大小写,url
category: Nginx
status: publist
summary: Nginx Url忽略大小写匹配。 
-->

### 写在前面 ###
使用Nginx做反向代理服务器的时候,我们偶尔会有希望用户无论使用大写还是小写,  
都匹配到同一个代理,这里就用到了忽略大小写匹配正则

### Nginx具体配置 ###
```
upstream lb_test01 {
    server 10.0.0.1:7002 weight=10 max_fails=3 fail_timeout=15s;
    server 10.0.0.1:7002 weight=10 max_fails=3 fail_timeout=15s;
}
upstream lb_test02 {
    server 10.0.0.1:7005 weight=10 max_fails=3 fail_timeout=15s;
    server 10.0.0.1:7005 weight=10 max_fails=3 fail_timeout=15s;
}
upstream lb_test03 {
    server 10.0.0.1:7007 weight=10 max_fails=3 fail_timeout=15s;
    server 10.0.0.1:7007 weight=10 max_fails=3 fail_timeout=15s;
}
server {
    listen       80;
    server_name api.itttl.com;

    location /one {
        rewrite (?i)^/conf(.*) http://www.itttl.com/$1 permanent;
    }

    location ~* (?i)^/getcount {
        proxy_pass http://lb_actiontracker;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_redirect     off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        access_log  /data/logs/nginx/access/actiontracker.log;
    }
    location ~* (?i)^/getlist {
        proxy_pass http://lb_adtracker;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_redirect     off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        access_log  /data/logs/nginx/access/adtracker.log;

    }
}
```

### 测试 ###
```
#访问
http://api.itttl.com/getcount
http://api.itttl.com/getCount
http://api.itttl.com/GetCount
http://api.itttl.com/GETCOUNT

# 访问以上链接都能请求到lb_test01的upstream
```
