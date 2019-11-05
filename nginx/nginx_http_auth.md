<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-06-03
title: Nginx Web验证和根据IP开启/关闭Web验证
tags: nginx,Web验证
category: Nginx
status: publist
summary: Nginx Web验证和根据IP开启/关闭Web验证 
-->

### 写在前面 ###
Nginx Web验证很常用,但是加Web验证的同时总是希望有一些IP不需要验证。这里使用satisfy可以解决这个问题。  

### Nginx具体配置 ###
```
server {
    listen       80;
    server_name www.itttl.com;

    location / {
        satisfy any;
        allow 123.45.67.89;
        allow 123.45.67.88;
        auth_basic              "Restricted";
        auth_basic_user_file    /usr/local/nginx/conf/.htpasswd_www;

        proxy_pass http://127.0.0.1:19980;

        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_redirect     off;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    access_log  /usr/local/nginx/logs/www.log;
}
```

### 说明：### 

其中 satisfy 作用于所有的 access phase handler，参数值有两种：all 和 any，分别代表所有规则都满足和任一规则满足。将其配置为 any，并配合 auth_basic 和 access 模块就可以基于 IP 地址来开启/关闭认证。  

IP为123.45.67.89 和 123.45.67.88的client访问无需验证密码，其他IP的client都需要验证密码  

[Nginx官方文档][100]

[100]:http://nginx.org/en/docs/http/ngx_http_core_module.html#satisfy
