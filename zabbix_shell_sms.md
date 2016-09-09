<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-05-18
title: Zabbix 短信报警脚本
tags: zabbix,shell 
category: Zabbix
status: publist
summary: Zabbix 短信报警脚本
-->

![gitblog-logo](./img/logo_64x64.png)


## 直接上脚本
```
#!/bin/bash
#
# Filename:    sendSMS.sh
# Revision:    1.0
# Date:        2015/08/27
# Author:      
# Email:       sdata@foxmail.com
# Description: zabbix短信告警脚本
 
# 脚本的日志文件
LOGFILE="/tmp/SMS.log"
>"$LOGFILE"
exec 1>"$LOGFILE"
exec 2>&1
 
# Uid 网站用户名
# Key 接口秘钥
Uid="itttl-user"
Key="itttl-passwd"
 
MOBILE_NUMBER=$1    # 手机号码
MESSAGE_FUCK=$3        # 短信内容
MESSAGE_UTF8=`iconv -t GB2312 -f UTF-8 << EOF
$MESSAGE_FUCK
EOF`
XXD="/usr/bin/xxd"
CURL="/usr/bin/curl"
TIMEOUT=5
 
# 短信内容要经过URL编码处理，除了下面这种方法，也可以用curl的--data-urlencode选项实现。
MESSAGE_ENCODE=$(echo "$MESSAGE_UTF8" | ${XXD} -ps | sed 's/\(..\)/%\1/g' | tr -d '\n')
# SMS API
URL="http://123.45.67.89:9876/QxtSms/QxtFirewall?OperID=${Uid}&OperPass=${Key}&SendTime=&ValidTime=&AppendID=0000&DesMobile=${MOBILE_NUMBER}&Content=${MESSAGE_ENCODE}&ContentType=8"
# Send it
set -x
${CURL} -s --connect-timeout ${TIMEOUT} "${URL}"

```
