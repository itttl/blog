<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-05-18
title: Zabbix 微信报警脚本
tags: zabbix,shell,weixin
category: Zabbix
status: publist
summary: Zabbix 微信报警脚本
-->

![gitblog-logo](./img/logo_64x64.png)


## 直接上脚本
```
#!/bin/bash
#
# Filename:    sendWeiXin.sh
# Revision:    1.0
# Date:        2016/05/08
# Author:      yanliang.zhao
# Email:       sdata@foxmail.com
# Description: zabbix微信报警脚本

CropID='wxxxxxxxxxxxxxxxxx'
Secret='XXXXXXXXXXXXXXXXXXXXXX-XXXXX-XXXXXXXXXXXXXXXXX-XXXXXXXXXXXXXXXXX'
GURL="https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid=$CropID&corpsecret=$Secret" 
Gtoken=$(/usr/bin/curl -s -G $GURL | awk -F\" '{print $4}')

PURL="https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=$Gtoken"

function body() {
        local int AppID=6                       # 企业号中的应用id
        local UserID=$1                         # 部门成员id，zabbix中定义的微信接收者
        local PartyID=                         # 部门id，定义了范围，组内成员都可接收到消息
        local Msg=$(echo "$@" | cut -d" " -f3-) # 过滤出zabbix中传递的第三个参数
        printf '{\n'
        printf '\t"touser": "'"$UserID"'",\n'
        printf '\t"toparty": "'"$PartyID"'",\n'
        printf '\t"msgtype": "text",\n'
        printf '\t"agentid": '"$AppID"',\n'
        printf '\t"text": {\n'
        printf '\t\t"content": "'"$Msg"'"\n'
        printf '\t},\n'
        printf '\t"safe":"0"\n'
        printf '}\n'
}
/usr/bin/curl --data-ascii "$(body $1 $2 $3)" $PURL
#body $1 $2 $3

```
