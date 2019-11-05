cd /data
### 源码
git clone ……
ls /data/superset

### python3.6虚拟机
python3 -m venv superset-dl-py3
ls /data/superset-dl-py36/

 1018  vim /data/superset-py3/lib/python3.6/site-packages/superset/config.py
 1025  pip uninstall pandas
 1026  pip install pandas==0.23.4
 1027  fabmanager create-admin --app superset
 1028  superset db upgrade
 1029  pip list |grep sqlalchemy
 1030  pip mysql MySQL-python
 1031  pip mysql Python-MySQL
 1032  pip install MySQL-python
 1033  yum install mysql-devel -y
 1034  yum install python-devel -y
 1035  pip install MySQL-python
 1036  pip list |grep PyMySQL
 1037  which superset
 1038  superset db upgrade
 1039  vim /data/superset-py3/lib/python3.6/site-packages/superset/config.py
 1040  vim /data/superset-dl-py36/lib/python3.6/site-packages/superset/config.py
 1041  grep SQLALCHEMY_DATABASE_URI /data/superset-dl-py36/lib/python3.6/site-packages/superset/config.py
 1042  mysql -usuperset -p -h rds2-internal.deepleaper.com
 1043  pip install sqlalchemy==1.2.18
 1044  superset db upgrade
 1045  superset init
 1046  ls .env
 1047  cp .env /data/superset
 1048  ll
 1049  cd /data/superset
 1050  ls
 1051  cat .env
 1052  ls
 1053  ls run.py
 1054  cat run.py
 1055  ll
 1056  ls superset/config.py
 1057  vim cat superset/config.py
 1058  cat superset/config.py
 1059  vim superset/config.py
 1060  mv superset/config.py{,def}
 1061  cp /data/superset-dl-py36/lib/python3.6/site-packages/superset/config.py superset/
 1062  vim superset/config.py
 1063  netstat -antulp |grep 7088
 1064  ls
 1065  pwd
 1066  cd /etc/supervisord.d/
 1067  ls
 1068  cp superset.ini superset-.ini
 1069  mv superset-.ini superset-dl.ini
 1070  vim superset-dl.ini
 1071  ll /
 1072  ls /bigdata/superset-dl-py36/bin/python3.6
 1073  ll /bigdata/superset-dl-py36/bin/python3.6
 1074  vim superset-dl.ini
 1075  ls
 1076  supervisorctl update
 1077  vim superset-dl.ini
 1078  supervisorctl update
 1079  supervisorctl status
 1080  ifconfig
 1081  netstat -antulp|grep 7088
 1082  ll
 1083  cd /data/logs/superset/
 1084  tail -f superset-l
 1085  ll
 1086  supervisorctl update
 1087  supervisorctl status
 1088  vim /etc/supervisord.d/superset-dl.ini
 1089  supervisorctl update
 1090  supervisorctl status
 1091  ll
 1092  tail -f supervisor-dl_err.log
 1093  ls
 1094  cd /data/superset
 1095  ls
 1096  ll
 1097  ls requirements.txt
 1098  pip install -r requirements.txt
 1099  supervisorctl restart supetset-dl
 1100  supervisorctl restart superset-dl
 1101  supervisorctl status
 1102  tail -f supervisor-dl_err.log
 1103  ls
 1104  tail -f /data/logs/superset/supervisor-dl_err.log
 1105  pip list |grep pydruid
 1106  pip uninstall pydruid
 1107  cd /data/pydruid
 1108  ls
 1109  python3.6 setup.py install
 1110  cd ..
 1111  pip install pydruid 0.999.0.dev0
 1112  pip install pydruid==0.999.0.dev0
 1113  supervisorctl restart superset-dl
 1114  ifconfig
 1115  ll
 1116  cd superset-dl-py36/
 1117  ls
 1118  cd ..
 1119  cd superset
 1120  ls
 1121  cd superset/
 1122  ls
 1123  cd assets/


### 编译前段代码
cd superset/
curl omc.deepleaper.cn/scripts/auto-install/node/node_install.sh |sh
npm install
