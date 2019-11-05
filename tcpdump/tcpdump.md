<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2018-12-28
title: Tcpdump & Wireshark 抓包分析
tags: superset
category: tcpdump
status: publist
summary: Tcpdump & Wireshark 抓包分析
-->

# 开始
---

Windows：
1. 在 Wireshark 界面中，选择 Capture --> Interface ，选择对应连接 RDS 网卡后 --> Option --> 在 File 输入框中输入要保存的文件 rds.cap，然后点击 Start 开始抓包。
2. 复现问题。
3. 问题复现后，停止抓包。
注意： 网络抓包可能会产生大尺寸文件，建议考虑磁盘空间使用情况合理选择保存目录。

Linux 普通抓包:
1. 打开一个到 ECS 的 ssh 连接，并以 root 身份登录。
在该窗口运行下列命令
tcpdump -i eth0 -s 0 -w /var/tmp/rds.cap port 3306
2. 复现问题。
3. 使用 ctrl + c 终止上述窗口 的 tcpdump 命令。
注意： 网络抓包可能会产生大尺寸文件，建议考虑磁盘空间使用情况合理选择保存目录。

tcpdump -i eth0 -s 0 -w ./kafka.cap port 9092

## 分析
使用Wireshark更方便  


[t1][100]
[t2][101]

[100]:https://www.itttl.com/
[101]:https://www.itttl.com/
