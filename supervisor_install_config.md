<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-01-05
title: CentOS7 Supervisor安装配置
tags: supervisor
category: Supervisor
status: publist
summary: CentOS7 Supervisor安装配置
-->

![gitblog-logo](./img/logo_64x64.png)

## 一、准备

[官网][101]

### 1.介绍
Supervisor是一个进程控制系统. 它是一个C/S系统(注意: 其提供WEB接口给用户查询和控制), 它允许用户去监控和控制在类UNIX系统的进程. 它的目标与launchd, daemontools和runit有些相似, 但是与它们不一样的是, 它不是作为init(进程号pid是1)运行. 它是被用来控制进程, 并且它在启动的时候和一般程序并无二致. 

那么通俗点，它的作用是什么？你的Nginx，Tomcat，memcache，Redis...会崩么，不会？好吧，那你自己写的服务器监测脚本呢？好吧，不要再纠结了，交给Supervisor吧，它会帮你维护这些，即使它们不小心崩了，Supervisor会帮你看住它们，维护它们。

### 2.添加epel安装源
-----
这里使用阿里云的epel源   
```
cat > /etc/yum.repos.d/epel.repo < EOF
[epel]
name=Extra Packages for Enterprise Linux 7 - $basearch
baseurl=http://mirrors.aliyun.com/epel/7/$basearch
http://mirrors.aliyuncs.com/epel/7/$basearch
failovermethod=priority
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
 
[epel-debuginfo]
name=Extra Packages for Enterprise Linux 7 - $basearch - Debug
baseurl=http://mirrors.aliyun.com/epel/7/$basearch/debug
http://mirrors.aliyuncs.com/epel/7/$basearch/debug
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=0
 
[epel-source]
name=Extra Packages for Enterprise Linux 7 - $basearch - Source
baseurl=http://mirrors.aliyun.com/epel/7/SRPMS
http://mirrors.aliyuncs.com/epel/7/SRPMS
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=0
EOF
```
## 二、安装supervisor
-----
```
yum install supervisor -y
```


## 三、配置
-----
### 1.(program)配置模板
```
$ cat /etc/supervisord.d/mod.ini

[program:cat]
command=/bin/cat
process_name=%(program_name)s
numprocs=1
directory=/tmp
umask=022
priority=999
autostart=true
autorestart=true
startsecs=10
startretries=3
exitcodes=0,2
stopsignal=TERM
stopwaitsecs=10
user=chrism
redirect_stderr=false
stdout_logfile=/a/path
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_capture_maxbytes=1MB
stderr_logfile=/a/path
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_capture_maxbytes=1MB
environment=A="1",B="2"
serverurl=AUTO
```
### 2.简化模板
```
cat /etc/supervisord.d/motor.ini 
[program:motor]
command=java -jar motor.jar
directory=/data/motor
numprocs=1
process_name=%(program_name)s
priority=999
umask=022
startsecs=2
startretries=3
exitcodes=0,2
autorestart=true
stopsignal=INT
user=ecu
stdout_logfile=/var/log/supervisor/motor.log
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_capture_maxbytes=1MB
stderr_logfile=/var/log/supervisor/motor_err.log
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_capture_maxbytes=1MB
```
### 3.(program)配置说明

```
;*为必须填写项  
;*[program:应用名称]  
[program:cat]  

;*命令路径,如果使用python启动的程序应该为 python /home/test.py,  
;不建议放入/home/user/, 对于非user用户一般情况下是不能访问  
command=/bin/cat  

;当numprocs为1时,process_name=%(program_name)s  
;当numprocs>=2时,%(program_name)s_%(process_num)02d  
process_name=%(program_name)s  

;进程数量  
numprocs=1  

;执行目录,若有/home/supervisor_test/test1.py
;将directory设置成/home/supervisor_test
;则command只需设置成python test1.py
;否则command必须设置成绝对执行目录
directory=/tmp

;掩码:--- -w- -w-, 转换后rwx r-x w-x
umask=022

;优先级,值越高,最后启动,最先被关闭,默认值999
priority=999

;如果是true,当supervisor启动时,程序将会自动启动
autostart=true

;*自动重启
autorestart=true

;启动延时执行,默认1秒
startsecs=10

;启动尝试次数,默认3次
startretries=3

;当退出码是0,2时,执行重启,默认值0,2
exitcodes=0,2

;停止信号,默认TERM
;中断:INT(类似于Ctrl+C)(kill -INT pid),退出后会将写文件或日志(推荐)
;终止:TERM(kill -TERM pid)
;挂起:HUP(kill -HUP pid),注意与Ctrl+Z/kill -stop pid不同
;从容停止:QUIT(kill -QUIT pid)
;KILL, USR1, USR2其他见命令(kill -l),说明1
stopsignal=TERM

stopwaitsecs=10

;*以root用户执行
user=root

;重定向
redirect_stderr=false

stdout_logfile=/a/path
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_capture_maxbytes=1MB
stderr_logfile=/a/path
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_capture_maxbytes=1MB

;环境变量设置
environment=A="1",B="2"

serverurl=AUTO
```
### Section Example
http://supervisord.org/configuration.html
```
[fcgi-program:fcgiprogramname]
command=/usr/bin/example.fcgi
socket=unix:///var/run/supervisor/%(program_name)s.sock
socket_owner=chrism
socket_mode=0700
process_name=%(program_name)s_%(process_num)02d
numprocs=5
directory=/tmp
umask=022
priority=999
autostart=true
autorestart=unexpected
startsecs=1
startretries=3
exitcodes=0,2
stopsignal=QUIT
stopasgroup=false
killasgroup=false
stopwaitsecs=10
user=chrism
redirect_stderr=true
stdout_logfile=/a/path
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_events_enabled=false
stderr_logfile=/a/path
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_events_enabled=false
environment=A="1",B="2"
serverurl=AUTO
```



### 4.(inet_http_server)配置说明
可以使用浏览器查看和控制进程状态
```
[inet_http_server]         ; inet (TCP) server disabled by default
port=0.0.0.0:9001          ; (ip_address:port specifier, *:port for all iface)
username=user              ; 用户名 (default is no username (open server))
password=123               ; 密码 (default is no password (open server))
```
## 四、启动与关闭
-----
### 1. 启动supervisord
$ supervisord -c /etc/supervisord.conf
### 2. 关闭supervisord
$ supervisorctl shutdown
### 3. 重新载入配置
$ supervisorctl reload

### PS说明:

常用信号说明-原文
```
信号名称    数字表示    说明
SIGHUP  1   终端挂起或控制进程终止。当用户退出Shell时，由该进程启动的所有进程都会收到这个信号，默认动作为终止进程。
SIGINT  2   键盘中断。当用户按下组合键时，用户终端向正在运行中的由该终端启动的程序发出此信号。默认动作为终止进程。
SIGQUIT 3   键盘退出键被按下。当用户按下或组合键时，用户终端向正在运行中的由该终端启动的程序发出此信号。默认动作为退出程序。
SIGFPE  8   发生致命的运算错误时发出。不仅包括浮点运算错误，还包括溢出及除数为0等所有的算法错误。默认动作为终止进程并产生core文件。
SIGKILL 9   无条件终止进程。进程接收到该信号会立即终止，不进行清理和暂存工作。该信号不能被忽略、处理和阻塞，它向系统管理员提供了可以杀死任何进程的方法。
SIGALRM 14  定时器超时，默认动作为终止进程。
SIGTERM 15  程序结束信号，可以由 kill 命令产生。与SIGKILL不同的是，SIGTERM 信号可以被阻塞和终止，以便程序在退出前可以保存工作或清理临时文件等。
$ kill -l
 1) SIGHUP     2) SIGINT     3) SIGQUIT     4) SIGILL     5) SIGTRAP
 6) SIGABRT     7) SIGBUS     8) SIGFPE     9) SIGKILL    10) SIGUSR1
11) SIGSEGV    12) SIGUSR2    13) SIGPIPE    14) SIGALRM    15) SIGTERM
16) SIGSTKFLT    17) SIGCHLD    18) SIGCONT    19) SIGSTOP    20) SIGTSTP
21) SIGTTIN    22) SIGTTOU    23) SIGURG    24) SIGXCPU    25) SIGXFSZ
26) SIGVTALRM    27) SIGPROF    28) SIGWINCH    29) SIGIO    30) SIGPWR
31) SIGSYS    34) SIGRTMIN    35) SIGRTMIN+1    36) SIGRTMIN+2    37) SIGRTMIN+3
38) SIGRTMIN+4    39) SIGRTMIN+5    40) SIGRTMIN+6    41) SIGRTMIN+7    42) SIGRTMIN+8
43) SIGRTMIN+9    44) SIGRTMIN+10    45) SIGRTMIN+11    46) SIGRTMIN+12    47) SIGRTMIN+13
48) SIGRTMIN+14    49) SIGRTMIN+15    50) SIGRTMAX-14    51) SIGRTMAX-13    52) SIGRTMAX-12
53) SIGRTMAX-11    54) SIGRTMAX-10    55) SIGRTMAX-9    56) SIGRTMAX-8    57) SIGRTMAX-7
58) SIGRTMAX-6    59) SIGRTMAX-5    60) SIGRTMAX-4    61) SIGRTMAX-3    62) SIGRTMAX-2
```

## 五、操作详解
-----
### 1.启停某进程
```
supervisorctl start motor
supervisorctl stop motor
supervisorctl restart motor
```
### 2.重新加载所有supervisor管理的进程
```
supervisorctl reload
```
## 六、测试
-----
查看服务是否正常即可


[参考博客][102]
[101]:http://supervisord.org/
[102]:http://my.oschina.net/crooner/blog/395069?fromerr=80bx38vO
