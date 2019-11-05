<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-11-16
title: Nginx代理上网
tags: nginx,代理上网,http_proxy
category: Nginx
status: publist
summary: Ningx代理局域网内其他Linux服务器上网。 
-->

### 写在前面 ###
反向代理的工具很多,squid也可以做到，这里介绍的是Nginx代理上网。

### Nginx具体配置 ###
```
server{
   resolver 114.114.114.114;
   listen 8002;
   location / {
        proxy_pass http://$host$request_uri;
        proxy_redirect off;
        proxy_set_header        Host    $host;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        allow 10.10.0.0/16;
        deny all;
   }
}
```
### 注意项：### 
- 1. 不能有hostname 
- 2. 必须有resolver, 即dns，即上面的x.x.x.x，换成你们的DNS服务器ip即可 
- 3 . $http_host和$request_uri是nginx系统变量，不要想着替换他们，保持原样就OK。 

#### 查看dns方法 ####
```
cat /etc/resolv.conf 
```

### 测试 ###

##### 在需要访问外网的机器上执行以下操作之一即可： 
```
#临时生效,命令行执行后即可
export http_proxy=http://yourproxyaddress:proxyport 
#永久生效,在文件/etc/.bashrc下追加如下内容:
export http_proxy=http://yourproxyaddress:proxyport 
yourproxyaddress也就是你的Nginx服务器的ip了，proxyport就是上面配置中的8002，可以根据自己的需要修改。 
```
### 填坑
#### 问题
Nginx不读取本地hosts文件了,所有请求Nginx的域名都不走本地hosts而是走dns解析。坑啊！！！这里是Nginx服务器。

#### 解决
这台服务器使用了代理上网，也就是配置了环境变量：export http_proxy=http://proxy_server:8085  
导致所有的请求都走了代理服务器，所有域名也走了代理服务器指定的DNS。
删除即可，解决

