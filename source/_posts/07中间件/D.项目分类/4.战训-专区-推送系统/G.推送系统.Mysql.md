docker exec -it mysql_prod_papsystem_mysql_01_1 /bin/bash

mysql -P 3309 -uroot -p


mysql  -uroot -pLibng@0510

use mysql;

select user,host from user;

ALTER USER'root'@'%' IDENTIFIED WITH mysql_native_password BY 'Libng@0510'; 
update user set host='%' where user='root';
FLUSH PRIVILEGES;

##如果plugin 的加密方式为caching_sha2_password，表示需要更改
##更改root用户加密方式
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by '123456';
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
FLUSH PRIVILEGES;




---------------------------
```shell
[root@idszt-node101 ~]# df -h
文件系统                 容量  已用  可用 已用% 挂载点
devtmpfs                  47G     0   47G    0% /dev
tmpfs                     47G     0   47G    0% /dev/shm
tmpfs                     47G   84M   47G    1% /run
tmpfs                     47G     0   47G    0% /sys/fs/cgroup
/dev/mapper/centos-root  100G  4.3G   96G    5% /
/dev/sda2               1014M  185M  830M   19% /boot
/dev/mapper/centos-home   50G   37M   50G    1% /home
/dev/mapper/centos-var   100G  3.5G   97G    4% /var
/dev/mapper/centos-data  1.4T  8.9G  1.4T    1% /data
tmpfs                    9.4G   44K  9.4G    1% /run/user/0
overlay                  100G  3.5G   97G    4% /var/lib/docker/overlay2/1d2f66ac60c0f8ce174b254f81f1f80c3a5c4bc1cc60482643809a7a9f76cb4a/merged
shm                       64M     0   64M    0% /var/lib/docker/containers/f8d4e34481169561689cc7e1142a2dd8e04d23422951d9866d4c1c3e23c530c6/mounts/shm
overlay                  100G  3.5G   97G    4% /var/lib/docker/overlay2/07328ea1ad64a11764227fdf9806ffc3c932a8c067958858d5d6f94a4e72b4b7/merged
shm                       64M     0   64M    0% /var/lib/docker/containers/0c40b0d579becf058f8ef3b0c65198a457c11c225a5225a86c6a00b8f08ef86a/mounts/shm
[root@idszt-node101 ~]# cd /data/program/mysql/
[root@idszt-node101 mysql]# ls
mysql-8.0.32-1.el7.x86_64.rpm-bundle.tar
[root@idszt-node101 mysql]# tar -xvf mysql-8.0.32-1.el7.x86_64.rpm-bundle.tar
mysql-community-client-8.0.32-1.el7.x86_64.rpm
mysql-community-client-plugins-8.0.32-1.el7.x86_64.rpm
mysql-community-common-8.0.32-1.el7.x86_64.rpm
mysql-community-debuginfo-8.0.32-1.el7.x86_64.rpm
mysql-community-devel-8.0.32-1.el7.x86_64.rpm
mysql-community-embedded-compat-8.0.32-1.el7.x86_64.rpm
mysql-community-icu-data-files-8.0.32-1.el7.x86_64.rpm
mysql-community-libs-8.0.32-1.el7.x86_64.rpm
mysql-community-libs-compat-8.0.32-1.el7.x86_64.rpm
mysql-community-server-8.0.32-1.el7.x86_64.rpm
mysql-community-server-debug-8.0.32-1.el7.x86_64.rpm
mysql-community-test-8.0.32-1.el7.x86_64.rpm
[root@idszt-node101 mysql]# rpm  -ivh  *.rpm --nodeps --force
警告：mysql-community-client-8.0.32-1.el7.x86_64.rpm: 头V4 RSA/SHA256 Signature, 密钥 ID 3a79bd29: NOKEY
准备中...                          ################################# [100%]
正在升级/安装...
   1:mysql-community-common-8.0.32-1.e################################# [  8%]
   2:mysql-community-client-plugins-8.################################# [ 17%]
   3:mysql-community-libs-8.0.32-1.el7################################# [ 25%]
   4:mysql-community-client-8.0.32-1.e################################# [ 33%]
   5:mysql-community-icu-data-files-8.################################# [ 42%]
   6:mysql-community-server-8.0.32-1.e################################# [ 50%]
   7:mysql-community-server-debug-8.0.################################# [ 58%]
   8:mysql-community-test-8.0.32-1.el7################################# [ 67%]
   9:mysql-community-devel-8.0.32-1.el################################# [ 75%]
  10:mysql-community-libs-compat-8.0.3################################# [ 83%]
  11:mysql-community-embedded-compat-8################################# [ 92%]
  12:mysql-community-debuginfo-8.0.32-################################# [100%]
[root@idszt-node101 mysql]# systemctl enable mysqld
[root@idszt-node101 mysql]# systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html




[root@idszt-node101 mysql]# systemctl start mysqld
[root@idszt-node101 mysql]# sudo grep 'temporary password' /var/log/mysqld.log
2023-08-02T09:32:28.941314Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: etsbuZ3uS5/<

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'mysql#SZT8734';
Query OK, 0 rows affected (0.01 sec)


mysql>  use mysql;
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
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> select user,host from user;
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| root             | %         |
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
+------------------+-----------+
4 rows in set (0.00 sec)

mysql> exit
Bye
[root@idszt-node101 mysql]# vi /etc/my.cnf
[root@idszt-node101 mysql]# cat /etc/my.cnf






```


### 用户设置

CREATE USER `monitor`@`localhost` IDENTIFIED WITH caching_sha2_password BY 'mysql!SZT5321' PASSWORD EXPIRE NEVER;

GRANT `root`@`%` TO `monitor`@`localhost`;

SET DEFAULT ROLE `root`@`%` TO `monitor`@`localhost`;