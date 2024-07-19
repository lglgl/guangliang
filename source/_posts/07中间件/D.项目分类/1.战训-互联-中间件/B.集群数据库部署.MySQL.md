- 参考官网：https://dev.mysql.com/doc/refman/8.0/en/linux-installation-rpm.html

### 上传安装包

```shell
#安装包mysql-8.0.32-1.el7.x86_64.rpm-bundle.tar上传至/home/idszt/mysql_packages目录

mkdir /home/idszt/mysql_packages && cd /home/idszt/mysql_packages

tar -xvf mysql-8.0.32-1.el7.x86_64.rpm-bundle.tar
mv mysql-community* /root/mysql_packages && cd /root/mysql_packages


```



### 安装MySQL

```shell
[root@node5 ~]# rpm  -ivh  *.rpm --nodeps --force
警告：mysql-community-client-8.0.32-1.el7.x86_64.rpm: 头V4 RSA/SHA256 Signature, 密钥 ID 3a79bd29: NOKEY
  12:mysql-community-debuginfo-8.0.32-################################# [100%]
```



### 配置初始密码
systemctl enable mysqld
systemctl status mysqld
systemctl stop mysqld
```shell
[root@node5 ~]# systemctl restart mysqld
[root@node5 ~]# sudo grep 'temporary password' /var/log/mysqld.log
2023-06-19T06:27:07.286029Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: jK5K/g=la/=y
[root@node5 ~]# mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.32

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'mysql#SZT2132';
Query OK, 0 rows affected (0.01 sec)
```



### 开启远程连接

```shell
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select user,host from user;
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
| root             | localhost |
+------------------+-----------+
4 rows in set (0.00 sec)

mysql> update user set host='%' where user='root';
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)

mysql> select user,host from user;
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| root             | %         |
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
+------------------+-----------+
4 rows in set (0.01 sec)

```



### 允许防火墙访问

```shell
[root@node5 mysql_packages]# systemctl  start firewalld
[root@node5 ~]# firewall-cmd --zone=public --add-port=3309/tcp --permanent
success
[root@node5 ~]# firewall-cmd --reload
success
[root@node5 ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens32
  sources:
  services: dhcpv6-client ssh
  ports: 6379/tcp 6380/tcp 16379/tcp 3306/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

### 配置双主

两台主机均创建用户

```
mysql> CREATE USER 'repl'@'192.168.240.27' IDENTIFIED WITH mysql_native_password BY 'repl@BAS8796';
mysql> GRANT REPLICATION SLAVE ON *.* TO  'repl'@'192.168.240.27' ;
mysql> flush privileges;
```

/etc/my.cnf 添加以下配置，注意修改server-id和master-host

```
log-bin=bin-log
server-id=186

[client-server]

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
binlog-ignore-db=mysql,test,infromation_schema,sys,performance_schema
binlog-do-db=test1
master-host=192.168.240.26
master-port=3306
master-user=repl
master-pass=repl@SZT2021
master-retry-count=999
master-connect-retry=60
```



```
systemctl restart mysqld && systemctl status mysqld

# 192.168.10.185操作
mysql> SHOW MASTER STATUS;
+---------------+----------+--------------+------------------+-------------------+
| File          | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+---------------+----------+--------------+------------------+-------------------+
| binlog.000001 |     2096 |              |                  |                   |
+---------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)


STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='192.168.240.26',MASTER_USER='repl',MASTER_PORT=3309,MASTER_PASSWORD='repl@BAS8796',MASTER_LOG_FILE='binlog.000003',MASTER_LOG_POS=157;
START SLAVE;


#192.168.10.186操作
mysql> SHOW MASTER STATUS;
+---------------+----------+--------------+------------------+-------------------+
| File          | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+---------------+----------+--------------+------------------+-------------------+
| binlog.000001 |     2096 |              |                  |                   |
+---------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)

STOP SLAVE;
CHANGE MASTER TO MASTER_HOST='192.168.10.185',MASTER_USER='repl',MASTER_PASSWORD='repl@SZT2021',MASTER_LOG_FILE='bin-log.000001',MASTER_LOG_POS=2096;
START SLAVE;
```



### 配置keepalived

安装keepalived
yum install -y keepalived


配置keepalived
node185
```
! Configuration File for keepalived

global_defs {
   router_id idszt-node005
}

vrrp_instance VI_1 {
    state MASTER
    interface ens32
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.10.75
    }
}

virtual_server 192.168.10.75 3306 {
    delay_loop 6
    lb_algo rr
    lb_kind DR
    net_mask 255.255.255.0
    persistence_timeout 50
    protocol TCP

    real_server 192.168.10.185 3306 {
        weight 1
        notify_down /etc/keepalived/closekeepalived.sh
        TCP_CHECK {
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 3
            connect_port 3306
        }
    }
    
}
```
node186
```
! Configuration File for keepalived

global_defs {
   router_id idszt-node006
}

vrrp_instance VI_1 {
    state BACKUP
    interface ens32
    virtual_router_id 51
    priority 50
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.10.175
    }
}

virtual_server 192.168.10.175 3306 {
    delay_loop 6
    lb_algo rr
    lb_kind DR
    net_mask 255.255.255.0
    persistence_timeout 50
    protocol TCP

    real_server 192.168.10.186 3306 {
        weight 1
        notify_down /etc/keepalived/closekeepalived.sh
        TCP_CHECK {
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 3
            connect_port 3306
        }
    }
    
}
```

### 配置切换

echo "sudo -S killall keepalived" >/etc/keepalived/closekeepalived.sh
chmod +x /etc/keepalived/closekeepalived.sh

### 验证
此次验证失败，玄学！



### 配置免密
```shell
[root@node5 .ssh]# ssh-copy-id idszt-node006
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
The authenticity of host 'idszt-node006 (192.168.10.186)' can't be established.
ECDSA key fingerprint is SHA256:spFkAePyLM4alcHcJuHBBy5EhAvhtsRt5z0U/f4c66w.
ECDSA key fingerprint is MD5:d0:cd:b2:34:47:12:75:79:6d:dc:31:0f:4e:4d:57:3a.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@idszt-node006's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'idszt-node006'"
and check to make sure that only the key(s) you wanted were added.

[root@node5 .ssh]# ssh-copy-id idszt-node008
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
The authenticity of host 'idszt-node008 (192.168.10.188)' can't be established.
ECDSA key fingerprint is SHA256:gRjdjbc/FFKcVyCL3WY73vAhymMrM4mEr3KDCSqKA8w.
ECDSA key fingerprint is MD5:97:d4:e4:98:72:0a:c3:bc:e9:b7:81:40:09:14:24:79.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@idszt-node008's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'idszt-node008'"
and check to make sure that only the key(s) you wanted were added.

[root@node5 .ssh]# ssh idszt-node006
Last login: Wed Jun 21 15:32:12 2023 from 192.168.10.189
[root@idszt-node006 ~]# exit
登出
Connection to idszt-node006 closed.


```