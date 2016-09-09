<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-08-12
title:修改zabbix为中文，并解决乱码问题
tags: zabbix
category: Zabbix
status: publist
summary:修改zabbix为中文，并解决乱码问题
-->

![gitblog-logo](./img/logo_64x64.png)

## 修改zabbix为中文
### 启用中文支持
```
vim /var/www/html/zabbix/include/locales.inc.php

##修改:
'zh_CN' => array('name' => _('Chinese (zh_CN)'),    'display' => false),
##为
'zh_CN' => array('name' => _('Chinese (zh_CN)'),    'display' => true),
```

### zabbix web页面修改设置;
```
profile-->user-->Language 选择Chinese(zh_CN)
```
刷新页面  


## 解决乱码

图形页面数据字段显示为方框或者乱码  

 - 解决如下

### 1.下载标准中文字体( simkai.ttf )
### 2.修改zabbix PHP页面指定字体
把它拷贝到zabbix的web端的fonts目录下例如：/var/www/html/zabbix/fonts/  
```
ls /var/www/html/zabbix/fonts/  
DejaVuSans.ttf  simkai.ttf  
```
### 3.修改php页面指定的字体文件：
```
vim /var/www/html/zabbix/include/defines.inc.php   

##修改
define('ZBX_GRAPH_FONT_NAME',  'DejaVuSans');为define('ZBX_GRAPH_FONT_NAME',       'DejaVuSans');
##为
define('ZBX_GRAPH_FONT_NAME',  'DejaVuSans');为define('ZBX_GRAPH_FONT_NAME',       'simkai');
```

保存，重启web，刷新zabbix web页面试试看，是不是恢复了。  
