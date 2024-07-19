
## 关闭防火墙限制

firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.216" port protocol="tcp" port="3309" accept'
firewall-cmd --reload


### 配置双主

两台主机均创建用户

business0,business1,business2,civil_data0,civil_data1,civil_data2,civil_manager,mh_business,mh_code,mh_verify,prod_nacos,push_xxl_job,xxl_job,xxl_job_verify

```
CREATE USER 'repl'@'%' IDENTIFIED WITH mysql_native_password BY 'mysql#SZT8632';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
flush privileges;
```

/etc/my.cnf 添加以下配置，注意修改server-id和master-host

```shell
## node007
log-bin=bin-log
server-id=217

[client-server]

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
binlog-ignore-db=mysql,test,infromation_schema,sys,performance_schema
binlog-do-db=business0,business1,business2,civil_data0,civil_data1,civil_data2,civil_manager,mh_business,mh_code,mh_verify,prod_nacos,push_xxl_job,xxl_job,xxl_job_verify
master-host=192.168.240.215
master-port=3309
master-user=repl
master-pass=mysql#SZT8632
master-retry-count=999
master-connect-retry=60

# node005
log-bin=bin-log
server-id=215

[client-server]
binlog-ignore-db=mysql,test,infromation_schema,sys,performance_schema
binlog-do-db=business0,business1,business2,civil_data0,civil_data1,civil_data2,civil_manager,mh_business,mh_code,mh_verify,prod_nacos,push_xxl_job,xxl_job,xxl_job_verify
master-host=192.168.240.217
master-port=3309
master-user=repl
master-pass=mysql#SZT8632
master-retry-count=999
master-connect-retry=60


# node006
log-bin=bin-log
server-id=216

[client-server]
binlog-ignore-db=mysql,test,infromation_schema,sys,performance_schema
binlog-do-db=business0,business1,business2,civil_data0,civil_data1,civil_data2,civil_manager,mh_business,mh_code,mh_verify,prod_nacos,push_xxl_job,xxl_job,xxl_job_verify
master-host=192.168.240.215
master-port=3309
master-user=repl
master-pass=mysql#SZT8632
master-retry-count=999
master-connect-retry=60



```



```
systemctl restart mysqld && systemctl status mysqld

# 192.168.240.217操作
mysql> SHOW MASTER STATUS;
bin-log.000003	794760

STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='192.168.240.215',MASTER_USER='repl',MASTER_PASSWORD='mysql#SZT8632',MASTER_PORT=3309,MASTER_LOG_FILE='bin-log.000002',MASTER_LOG_POS=425440832;
START SLAVE;


#192.168.240.215操作
mysql> SHOW MASTER STATUS;
bin-log.000002	425440832
STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='192.168.240.217',MASTER_USER='repl',MASTER_PASSWORD='mysql#SZT8632',MASTER_PORT=3309,MASTER_LOG_FILE='bin-log.000003',MASTER_LOG_POS=794760;
START SLAVE;



#192.168.240.216操作
mysql> SHOW MASTER STATUS;(查node102 MASTER_LOG_POS)
bin-log.000002	443474746
STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='192.168.240.215',MASTER_USER='repl',MASTER_PASSWORD='mysql#SZT8632',MASTER_PORT=3309,MASTER_LOG_FILE='bin-log.000002',MASTER_LOG_POS=425440832;
START SLAVE;
```


SHOW SLAVE STATUS;