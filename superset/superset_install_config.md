<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2018-12-28
title: Supserset 安装配置
tags: superset
category: Supserset
status: publist
summary: Supserset 安装配置
-->

## Supserset 安装配置 ###

---
### 一、环境准备
#### 1.源码
git clone https://github.com/apache/incubator-superset/

#### 2.安装依赖
```
sudo yum upgrade -y python-setuptools
sudo yum install -y gcc gcc-c++ libffi-devel python-devel python-pip python-wheel openssl-devel libsasl2-devel openldap-devel
sudo yum -y install bzip2 bzip2-devel
```

#### 3.安装Python3.6
```
yum -y install python36 python36-devel
```
#### 4.建立 Python 虚拟环境
```
cd /opt
python3.6 -m venv py3-superset
source /opt/py3-superset/bin/activate

# 看到下面的提示符代表成功,以后运行 Jumpserver 都要先运行以上 source 命令,以下所有命令均在该虚拟环境中运行
(py3-superset) [root@dev incubator-superset]
```
#### 5.自动载入 Python 虚拟环境配置

此项仅为懒癌晚期的人员使用,防止运行 Superset 时忘记载入 Python 虚拟环境导致程序无法运行。使用autoenv
```
cd /opt
git clone https://github.com/kennethreitz/autoenv.git
echo 'source /opt/autoenv/activate.sh' >> ~/.bashrc
source ~/.bashrc

# 进入 incubator-superset 目录时将自动载入python 虚拟环境
echo "source /opt/py3-superset/bin/activate" > /data/incubator-superset/.env
cd /data/incubator-superset/
#首次进入会提示加载Py环境变量的确认信息，按y继续即可
```

### 二、开始安装superset


```
pip install --upgrade setuptools pip
```
#### Install superset
```
pip install superset
```
#### Create an admin user (you will be prompted to set a username, first and last name before setting a password)
```
fabmanager create-admin --app superset
```
#### Initialize the database
```
superset db upgrade
```
#### Load some data to play with
```
superset load_examples
```

#### Create default roles and permissions
```
superset init
```

#### To start a development web server on port 8088, use -p to bind to another port
```
superset runserver -d
```



[Superset官方文档][100]
[incubator-superset源码][101]

[100]:https://superset.incubator.apache.org/installation.html
[101]:https://github.com/apache/incubator-superset
