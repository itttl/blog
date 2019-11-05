<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-06-3
title: Python fileinput文本处理
tags: python,fileinput
category: Python
status: publist
summary: Python fileinput文本处理
-->

![gitblog-logo](./img/logo_64x64.png)


## fileinput模块介绍
fileinput模块可以对一个或多个文件中的内容进行迭代、遍历等操作。该模块的input()函数有点类似文件  
readlines()方法，区别在于前者是一个迭代对象，需要用for循环迭代，后者是一次性读取所有行。  
用fileinput对文件进行循环遍历，格式化输出，查找、替换等操作，非常方便。  

### 简单用法
```
import fileinput
for line in fileinput.input():
    process(line)
```
### 格式
```
fileinput.input([files[, inplace[, backup[, bufsize[, mode[, openhook]]]]]])
```
### 默认格式
```
fileinput.input (files=None, inplace=False, backup='', bufsize=0, mode='r', openhook=None)

files:                  #文件的路径列表，默认是stdin方式，多文件['1.txt','2.txt',...]
inplace:                #是否将标准输出的结果写回文件，默认不取代
backup:                 #备份文件的扩展名，只指定扩展名，如.bak。如果该文件的备份文件已存在，则会自动覆盖。
bufsize:                #缓冲区大小，默认为0，如果文件很大，可以修改此参数，一般默认即可
mode:                   #读写模式，默认为只读
openhook:               #该钩子用于控制打开的所有文件，比如说编码方式等;

```
### 常用函数
```
fileinput.input()       #返回能够用于for循环遍历的对象
fileinput.filename()    #返回当前文件的名称
fileinput.lineno()      #返回当前已经读取的行的数量（或者序号）
fileinput.filelineno()  #返回当前读取的行的行号
fileinput.isfirstline() #检查当前行是否是文件的第一行
fileinput.isstdin()     #判断最后一行是否从stdin中读取
fileinput.close()       #关闭队列
```
## 举例
### 03/nginx.conf
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

### 例子01 读取文件内容
```
#!/usr/bin/env python
# coding:utf-8
import fileinput
for line in fileinput.input("03/nginx.conf"):
    print line,
```

### 例子02 处理文件
```
#!/usr/bin/env python
# coding:utf-8
import fileinput
for line in fileinput.input("03/nginx.conf",inplace=1,backup=".bak"):
    line=line.replace("www","blog")
    print line,
```

### 例子03 多文件处理
```
#!/usr/bin/env python
# coding:utf-8
import fileinput

def add_line_number(a,b):
    a=str(a)
    return a + "\t" + b
num=1
for line in fileinput.input(["03/nginx.conf","03/vhosts/blog.conf"],inplace=1,backup=".num"):
    print add_line_number(num,line),
    num=num+1

```
