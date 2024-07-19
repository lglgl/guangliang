
### 配置双主

两台主机均创建用户

```
CREATE USER 'repl'@'%' IDENTIFIED WITH mysql_native_password BY 'mysql#SZT7447';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
flush privileges;
```

/etc/my.cnf 添加以下配置，注意修改server-id和master-host

```shell
## node101
log-bin=bin-log
server-id=11

[client-server]

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
binlog-ignore-db=mysql,test,infromation_schema,sys,performance_schema
binlog-do-db=push_xxl_job,prod_papsystem_nacos
master-host=192.168.140.12
master-port=3309
master-user=repl
master-pass=mysql#SZT7447
master-retry-count=999
master-connect-retry=60

# node102
log-bin=bin-log
server-id=12

[client-server]
binlog-ignore-db=mysql,test,infromation_schema,sys,performance_schema
binlog-do-db=push_xxl_job,prod_papsystem_nacos
master-host=192.168.140.11
master-port=3309
master-user=repl
master-pass=mysql#SZT7447
master-retry-count=999
master-connect-retry=60


# node103
log-bin=bin-log
server-id=13

[client-server]
binlog-ignore-db=mysql,test,infromation_schema,sys,performance_schema
binlog-do-db=push_xxl_job,prod_papsystem_nacos
master-host=192.168.140.11
master-port=3309
master-user=repl
master-pass=mysql#SZT7447
master-retry-count=999
master-connect-retry=60



```



```
systemctl restart mysqld && systemctl status mysqld

# 192.168.10.185操作
mysql> SHOW MASTER STATUS;


STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='192.168.140.12',MASTER_USER='repl',MASTER_PASSWORD='mysql#SZT7447',MASTER_PORT=3309,MASTER_LOG_FILE='bin-log.000001',MASTER_LOG_POS=157;
START SLAVE;


#192.168.140.12操作
mysql> SHOW MASTER STATUS;

STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='192.168.140.11',MASTER_USER='repl',MASTER_PASSWORD='mysql#SZT7447',MASTER_PORT=3309,MASTER_LOG_FILE='bin-log.000001',MASTER_LOG_POS=157;
START SLAVE;



#192.168.140.13操作
mysql> SHOW MASTER STATUS;(查node102 MASTER_LOG_POS)

STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='192.168.140.12',MASTER_USER='repl',MASTER_PASSWORD='mysql#SZT7447',MASTER_PORT=3309,MASTER_LOG_FILE='bin-log.000001',MASTER_LOG_POS=800;
START SLAVE;
```
      MYSQL_SERVICE_HOST : 192.168.240.215