mysqldump -uroot -pmysql#SZT2132 -P3309 --all-databases > /data/backup/database/mysql/all.sql
mysqldump: [Warning] Using a password on the command line interface can be insecure.


Usage: mysqldump [OPTIONS] database [tables]
OR     mysqldump [OPTIONS] --databases [OPTIONS] DB1 [DB2 DB3...]
OR     mysqldump [OPTIONS] --all-databases [OPTIONS]
For more options, use mysqldump --help



### 导出
mysqldump -uroot -pmysql#SZT2132 -P3309 --databases business0 business1 business2 civil_data civil_data0 civil_data1 civil_data2 civil_manager  mh_business mh_code mh_verify prod_nacos push_xxl_job xxl_job xxl_job_verify > /data/backup/database/mysql/all.sql

### 导入

mysql -uusername -ppassword db1 <tb1tb2.sql


### 定时脚本
```shell

#!/bin/bash
#NAME:数据库备份

#设置本机数据库登录信息
mysql_user="root"
mysql_password="mysql#SZT2132"
mysql_host="localhost"
mysql_port="3309"
mysql_charset="utf8mb4"
date_time=`date +%Y-%m-%d-%H-%M`

#保存目录中的文件个数
count=30
#备份路径
path=/data/backup/database/mysql/data


#备份数据库sql文件并指定目录
mysqldump  --single-transaction --flush-logs --master-data=2 -h$mysql_host -u$mysql_user -p$mysql_password \
 --databases business0 business1 business2  civil_data0 civil_data1 civil_data2 civil_manager \
   mh_business mh_code mh_verify prod_nacos push_xxl_job xxl_job xxl_job_verify \
  > $path/$(date +%Y%m%d_%H:%M).sql


[ $? -eq 0 ] && echo "-----------------数据备份成功_$date_time-----------------" || echo "-----------------数据备份失败-----------------"

#找出需要删除的备份
delfile=`ls -l -crt $path/*.sql | awk '{print $9 }' | head -1`
#判断现在的备份数量是否大于阈值
number=`ls -l -crt $path/*.sql | awk '{print $9 }' | wc -l`
if [ $number -gt $count ]
then
          rm $delfile  #删除最早生成的备份，只保留count数量的备份
                                           #更新删除文件日志
            echo "-----------------已删除过去备份sql $delfile-----------------"
    fi

```


### 注释
```shell
-dump-slave

该选项将主的binlog位置和文件名追加到导出数据的文件中(show slave status)。设置为1时，将会以CHANGE MASTER命令输出到数据文件；设置为2时，会在change前加上注释。该选项将会打开--lock-all-tables，除非--single-transaction被指定。该选项会自动关闭--lock-tables选项。默认值为0。

mysqldump -uroot -p --all-databases --dump-slave=1

mysqldump -uroot -p --all-databases --dump-slave=2

--master-data

该选项将当前服务器的binlog的位置和文件名追加到输出文件中(show master status)。如果为1，将会输出CHANGE MASTER 命令；如果为2，输出的CHANGE  MASTER命令前添加注释信息。该选项将打开--lock-all-tables 选项，除非--single-transaction也被指定（在这种情况下，全局读锁在开始导出时获得很短的时间；其他内容参考下面的--single-transaction选项）。该选项自动关闭--lock-tables选项。

mysqldump -uroot -p --host=localhost --all-databases --master-data=1;

mysqldump -uroot -p --host=localhost --all-databases --master-data=2;


```


### 导入

mysql -h xxx.xxx.xxx.xxx:xxxx –u userName -p dbName < /tmp/dbName.sql
mysql -h localhost:3309 –u root -p 
-uroot -pmysql#SZT2132 -P3309

mysql -uroot -pmysql#SZT2132 -P3309  < 20230811_19_32.sql


统计qyqdb数据库下所有的表的行数，生产统计语句。
select concat(
    'select "', 
    TABLE_name, 
    '", count(*) from ', 
    TABLE_SCHEMA, 
    '.',
    TABLE_name,
    ' union all'
) from information_schema.tables 
where TABLE_SCHEMA='business0';


business0 business1 business2  civil_data0 civil_data1 civil_data2 civil_manager \
   mh_business mh_code mh_verify prod_nacos push_xxl_job xxl_job xxl_job_verify \