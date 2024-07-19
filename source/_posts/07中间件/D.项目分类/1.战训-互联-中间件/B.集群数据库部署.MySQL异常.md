
```sql

SHOW VARIABLES LIKE '%max_connections%';

Variable_name	Value
max_connections	200
mysqlx_max_connections	100



show status like 'Threads%';
Variable_name	Value
Threads_cached	7
Threads_connected	36
Threads_created	84
Threads_running	2

show global variables like '%max_connect_errors%';
Variable_name	Value
max_connect_errors	100

```


max_connect_errors = 2000
max_connections = 1000 


flush hosts;



mysql -uroot -pmysql#SZT2132 -P3309 -h192.168.240.217
mysql -unacos -pmysql#SZT3940 -P3309 -h192.168.240.217

mysql -unacos -pmysql#SZT3940 -P3309 -h192.168.140.11



1、将变量max_connection_errors的值设置为一个更大的值
set global max_connect_errors=1000;

2、执行命令使用flush hosts

3、改变系统变量值是的让MySQL服务器不记录host cache信息（不建议）如：set global host_cache_size=0;

select SUM_CONNECT_ERRORS as count from performance_schema.host_cache where ip='192.168.240.211'
select * from performance_schema.host_cache where ip='192.168.240.211'
---------------
 3. mysql字符集配置错误，导致无法插入数据库（Illegal mix of collations (latin1_swedish_ci,IMPLICIT) and (utf8mb4_general_ci,COERCIBLE) for operation）

在数据库执行查找的命令，判断条件含有特殊字符，再执行的时候报了以上错误；

查看字段编码发现是 latin1 字符集的latin1_german1_ci排序规则，而数据库是utf-8编码，确定错误是编码导致。

修改数据库字段为utf-8编码  再次执行 顺利查出结果




8月 31 17:59:40 BAS-YY07-Cent7.9 dbus[1215]: [system] Activating service name='org.fedoraproject.Setroubleshootd' (using servicehelper)
8月 31 17:59:40 BAS-YY07-Cent7.9 dbus[1215]: [system] Successfully activated service 'org.fedoraproject.Setroubleshootd'
8月 31 17:59:41 BAS-YY07-Cent7.9 setroubleshoot[17812]: SELinux is preventing /usr/sbin/mysqld from name_bind access on the tcp_socket port 3309. For complete SELinux messages run: sealert -l f94c825c-d778-
8月 31 17:59:41 BAS-YY07-Cent7.9 python[17812]: SELinux is preventing /usr/sbin/mysqld from name_bind access on the tcp_socket port 3309.

                                                 *****  Plugin bind_ports (92.2 confidence) suggests   ************************

                                                 If you want to allow /usr/sbin/mysqld to bind to network port 3309
                                                 Then you need to modify the port type.
                                                 Do
                                                 # semanage port -a -t PORT_TYPE -p tcp 3309
                                                     where PORT_TYPE is one of the following: mysqld_port_t, tram_port_t.

执行：
semanage port -a -t mysqld_port_t -p tcp 3309