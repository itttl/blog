<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-08-10
title: Docker 私有仓库搭建 
tags: docker,私有仓库,docker,registry,amazon,s3,oss
category: Docker
status: publist
summary: Docker私有仓库搭建,docker,registry,amazon,s3,oss
-->

![docker-logo](./img/logo_docker.png)

## 20170314更新:添加使用Amazon S3做镜像存储的私有仓库
## 20160518更新:使用阿里OSS的doceker私有仓库

### init web auth passwd and 自签名 ca  证书
```
htpasswd -Bbn itttl-name z5Zb6qefgGeKw2tA > config/htpasswd
docker exec registry htpasswd -Bbn itttl-name itttl-passwd > config/htpasswd

openssl req -newkey rsa:2048 -x509 -nodes -days 3560 -out config/chained.pem -keyout config/domain.key

```

### local-net && no web auth
```
#!/bin/bash
docker run -d -p 172.16.1.11:443:5000 --restart=always --name registry \
  -v `pwd`/config:/config \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/config/chained.pem \
  -e REGISTRY_HTTP_TLS_KEY=/config/domain.key \
  -e REGISTRY_STORAGE=oss \
  -e REGISTRY_STORAGE_OSS_ACCESSKEYID=XXXXXXXXXXXXXXXX \
  -e REGISTRY_STORAGE_OSS_ACCESSKEYSECRET=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX \
  -e REGISTRY_STORAGE_OSS_REGION=oss-cn-beijing \
  -e REGISTRY_STORAGE_OSS_BUCKET=docker-registry-name \
  -e REGISTRY_STORAGE_OSS_INTERNAL=false \
  -e REGISTRY_STORAGE_OSS_SECURE=false \
  registry:2.3
```
### wlan-net && web auth && 阿里 OSS 

```
#!/bin/bash
docker run -d -p 443:5000 --restart=always --name registry \
  -v `pwd`/config:/config \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/config/htpasswd \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/config/chained.pem \
  -e REGISTRY_HTTP_TLS_KEY=/config/domain.key \
  -e REGISTRY_STORAGE=oss \
  -e REGISTRY_STORAGE_OSS_ACCESSKEYID=XXXXXXXXXXXXXXXX \
  -e REGISTRY_STORAGE_OSS_ACCESSKEYSECRET=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX \
  -e REGISTRY_STORAGE_OSS_REGION=oss-cn-beijing \
  -e REGISTRY_STORAGE_OSS_BUCKET=docker-registry-name \
  -e REGISTRY_STORAGE_OSS_INTERNAL=ture \
  -e REGISTRY_STORAGE_OSS_SECURE=false \
  registry:2.3
```
### web auth && wlan-net && local store
```
#!/bin/bash
docker run -d -p 443:5000 --restart=always --name registry \
  -v `pwd`/config:/config \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/config/htpasswd \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/config/chained.pem \
  -e REGISTRY_HTTP_TLS_KEY=/config/domain.key \
  registry:2.3
```


### Amazon S3 && no auth 
```
#!/bin/bash
docker run -d -p 443:5000 --restart=always --name registry \
  -v `pwd`/config.s3:/config \
  -e "REGISTRY_HTTP_TLS_CERTIFICATE=/config/chained.pem" \
  -e "REGISTRY_HTTP_TLS_KEY=/config/domain.key" \
  -e "REGISTRY_STORAGE=s3" \
  -e "REGISTRY_STORAGE_S3_REGION=eu-west-1" \
  -e "REGISTRY_STORAGE_S3_BUCKET=itttl" \
  -e "REGISTRY_STORAGE_CACHE_BLOBDESCRIPTOR=inmemory" \
  -e "REGISTRY_STORAGE_S3_ACCESSKEY=XXXXXXXXXXXXXXXXXXXX" \
  -e "REGISTRY_STORAGE_S3_SECRETKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" \
  -e "SEARCHBACKEND=sqlalchemy" \
  registry:2
```
### 补充一个S3的授权策略
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation",
        "s3:ListBucketMultipartUploads"
      ],
      "Resource": "arn:aws:s3:::S3_BUCKET_NAME"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject",
        "s3:ListMultipartUploadParts",
        "s3:AbortMultipartUpload"
      ],
      "Resource": "arn:aws:s3:::S3_BUCKET_NAME/*"
    }
  ]
}
```
### 环境变量说明
```
REGISTRY_STORAGE_OSS_INTERNAL 阿里OSS是否使用内网
```


## /etc/host
10.0.0.10 registry 

##一、镜像下载
####docker.io中有现成的镜像registry
```
docker search registry
docker pull registry
```
### 二、启动 ###
```
docker run \

         -e SETTINGS_FLAVOR=s3 \

         -e AWS_BUCKET=mybucket \

         -e STORAGE_PATH=/data/registry \

         -e AWS_KEY=myawskey \

         -e AWS_SECRET=myawssecret \

         -e SEARCH_BACKEND=sqlalchemy \

         -p 5000:5000 \

         registry
```
### 资源限制启动        
```
docker run -d --name=registry --memory=1g --memory-swap=1g --cpuset-cpus=0,2 --cpu-shares=512  -p 5000:5000 -v /data/registry:/tmp/registry registry
```


## 三、私有仓库的使用
```
#vim /etc/sysconfig/docker
#私有仓库地址修改如下标签内容
OPTIONS='--graph=/data/docker --insecure-registry=10.0.0.10:5000'

service docker restart 
or
systemctl restart docker.service
```
### 打标签
```
docker tag qdsang/docker-nodejs-express 10.0.0.10:5000/nodejs-express:v1 
```
### 上传
```
docker push 10.0.0.10:5000/nodejs-express:v1  
```
### 搜索
```
docker search 10.0.0.10:5000/nodejs-express  
```
### 下载
```
docker pull 10.0.0.10:5000/nodejs-express:v1  
```

#### 每台服务器都修改成私有仓库很麻烦，而且如果想继续使用外部仓库会很麻烦，所以我们使用Nginx做代理并加入ssl

## 四、Nginx　代理
#### 这里默认你会安装配置Nginx服务器
```
htpasswd -c /usr/local/nginx/conf/docker-registry.htpasswd itttl

mkdir -p /usr/local/nginx/ssl/{certs,private}

chown nginx:nginx /usr/local/nginx/ssl

chown nginx:nginx /usr/local/nginx/conf/docker-registry.htpasswd  

chmod 600 /usr/local/nginx/conf/docker-registry.htpasswd  

openssl req -newkey rsa:2048 -x509 -nodes -days 3560 -out docker.itttl.com.crt -keyout docker.itttl.com.key  
```


### 五、Nginx　Config

```
upstream lb_docker { 

   server 10.0.0.10:5005; 

} 

server {

    listen  443;

    server_name docker.itttl.com;

    ssl on;

    ssl_certificate /usr/local/nginx/ssl/certs/docker.itttl.com.crt;

    ssl_certificate_key /usr/local/nginx/ssl/private/docker.itttl.com.key;

        ssl_session_timeout  5m;

        ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;

        #启用TLS1.1、TLS1.2要求OpenSSL1.0.1及以上版本，若您的OpenSSL版本低于要求，请使用 ssl_protocols TLSv1;

        ssl_ciphers  HIGH:!RC4:!MD5:!aNULL:!eNULL:!NULL:!DH:!EDH:!EXP:+MEDIUM;

        ssl_prefer_server_ciphers   on;

    location / {

        auth_basic              "Restricted";

        auth_basic_user_file    /usr/local/nginx/conf/docker-registry.htpasswd;

        proxy_pass http://lb_docker;

        proxy_set_header X-Real-IP $remote_addr;

        proxy_set_header Host $host;

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_redirect     off;

        proxy_http_version 1.1;

        proxy_set_header Upgrade $http_upgrade;

        proxy_set_header Connection "upgrade";

    }



 location /_ping {

     auth_basic off;

     proxy_pass http://lb_docker;

 } 

 location /v1/_ping {

     auth_basic off;

     proxy_pass http://lb_docker;

 }

 access_log  /usr/local/nginx/logs/docker.log;

}

```



## 六、Docker端配置
```
cat docker.itttl.com.crt >> /etc/pki/tls/certs/ca-bundle.crt  

systemctl restart docker.service　　

docker login https://docker.itttl.com



username:itttl  

passwd:***** 
```





