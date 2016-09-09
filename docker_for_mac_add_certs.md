<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-07-13
title: Docker for mac 添加私有证书(私有仓库验证证书)
tags: docker,mac
category: Docker
status: publist
summary: Docker for Mac 添加私有证书(私有仓库验证证书)
-->

![docker-logo](./img/logo_docker.png)

### (Solved) How to add your own self-signed certs to Docker for Mac (no more x509)

```
FINALLY figured out how to deal with Self Signed certs in docker for mac

1) Open the CLI mode:

screen ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty

2) Login as root (no password)

3) download custom certs

wget http://happytime.org/custom.crt
cat custom.crt >> /etc/ssl/certs/ca-certificates.crt
4) Restart the docker deamon

/etc/init.d/docker restart
```

[参考](https://forums.docker.com/t/solved-how-to-add-your-own-self-signed-certs-to-docker-for-mac-no-more-x509/16192)
