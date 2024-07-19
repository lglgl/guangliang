



echo linux#SZT3932 | passwd --stdin root;



systemctl enable mysqld
systemctl status mysqld
systemctl stop mysqld
systemctl start mysqld


1.查询所有数据库
show databases;

2.查询指定数据库中所有表名
select table_name from information_schema.tables where table_schema='database_name' and table_type='base table';

3.查询指定表中的所有字段名
select column_name from information_schema.columns where table_schema='database_name' and table_name='table_name';

4.查询指定表中的所有字段名和字段类型
select column_name,data_type from information_schema.columns where table_schema='database_name' and table_name='table_name';


nacos/nacos#SZT8854

### 查询连接
show processlist;
 
# 显示全部
show full processlist;


business0 business1 business2 civil_data0 civil_data1 civil_data2
civil_manager mh_business mh_code mh_verify prod_nacos push_xxl_job xxl_job xxl_job_verify

business0,business1,business2,civil_data0,civil_data1,civil_data2,civil_manager,mh_business,mh_code,mh_verify,prod_nacos,push_xxl_job,xxl_job,xxl_job_verify



1146 - Table 'xxl_job_verify.xxl_job_log_report' doesn't exist


select concat(
    'select "', 
    TABLE_name, 
    '", count(*) from ', 
    TABLE_SCHEMA, 
    '.',
    TABLE_name,
    ' union all'
) from information_schema.tables 
where 
TABLE_SCHEMA='business0' or 
TABLE_SCHEMA='business1' or
TABLE_SCHEMA='business2' or
TABLE_SCHEMA='civil_data0' or
TABLE_SCHEMA='civil_data1' or
TABLE_SCHEMA='civil_data2' or
TABLE_SCHEMA='civil_manager' or
TABLE_SCHEMA='mh_business' or
TABLE_SCHEMA='mh_code' or
TABLE_SCHEMA='mh_verify' or
TABLE_SCHEMA='prod_nacos' or
TABLE_SCHEMA='push_xxl_job' or
TABLE_SCHEMA='xxl_job' or
TABLE_SCHEMA='xxl_job_verify'