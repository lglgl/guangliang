```shell
sysbench --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root --mysql-password=mysql@SZT2021 /usr/share/sysbench/oltp_common.lua --tables=3 --table_size=100000000 prepare
```

官网优化建议：https://dev.mysql.com/doc/refman/8.0/en/optimizing-innodb-configuration-variables.html
配置文件：/etc/my.cnf
```shell
# 优化方向，缓存、日志

# 修改端口
port=3309

## 全局配置
# 字符集
character-set-server=utf8mb4
collation-server=utf8mb4_general_ci
# 时区
default-time-zone=+08:00
# 慢查询日志的开关，设置记录时间为2s
slow_query_log = 1
long_query_time = 2
# MySQL的最大连接数，预计连接数是50内
max_connections = 200


## 存储引擎相关
# InnoDB缓冲池缓存索引和原始数据
innodb_buffer_pool_size = 32G
# InnoDB为独立表空间模式，可以实现单表在不同的数据库中移动
innodb_file_per_table = 1
# 默认是300，调整为500，我们就二三十张表
innodb_open_files = 500
# 默认设置为0，不限制并发数
innodb_thread_concurrency = 0

## 日志相关
# 代表binlog的过期时间，30天
binlog_expire_logs_seconds = 259200
# 日志缓冲池相关
innodb_log_buffer_size = 128M
innodb_log_file_size = 512M
# 禁用压缩页面的日志记录
innodb_log_compressed_pages = off
```


### 压力测试
```shell
# 数据准备
sysbench --db-driver=mysql --time=30 --threads=10 --report-interval=1 --mysql-host=192.168.10.185 --mysql-port=3306 --mysql-user=root --mysql-password=mysql@SZT2021 --mysql-db=test_db --tables=20 --table_size=1000000 oltp_read_write --db-ps-mode=disable prepare


# 测试数据库的综合读写TPS
sysbench --db-driver=mysql --time=30 --threads=10 --report-interval=1 --mysql-host=192.168.10.185 --mysql-port=3306 --mysql-user=root --mysql-password=mysql@SZT2021 --mysql-db=test_db --tables=10 --table_size=1000000 oltp_read_write --db-ps-mode=disable run

[ 1s ] thds: 10 tps: 468.01 qps: 9507.90 (r/w/o: 6673.89/1888.01/946.00) lat (ms,95%): 31.94 err/s: 0.00 reconn/s: 0.00
[ 2s ] thds: 10 tps: 413.13 qps: 8225.63 (r/w/o: 5754.84/1644.53/826.26) lat (ms,95%): 36.24 err/s: 0.00 reconn/s: 0.00
[ 30s ] thds: 10 tps: 321.88 qps: 6509.52 (r/w/o: 4569.26/1295.51/644.75) lat (ms,95%): 46.63 err/s: 0.00 reconn/s: 0.00
SQL statistics:
    queries performed:
        read:                            135982
        write:                           38852
        other:                           19426
        total:                           194260
    transactions:                        9713   (323.36 per sec.)
    queries:                             194260 (6467.21 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          30.0358s
    total number of events:              9713

Latency (ms):
         min:                                   11.62
         avg:                                   30.90
         max:                                  130.70
         95th percentile:                       49.21
         sum:                               300122.50

Threads fairness:
    events (avg/stddev):           971.3000/7.38
    execution time (avg/stddev):   30.0122/0.01




# 只读测试
sysbench --db-driver=mysql --time=30 --threads=16 --report-interval=1 --mysql-host=192.168.10.185 --mysql-port=3306 --mysql-user=root --mysql-password=mysql@SZT2021 --mysql-db=test_db --tables=20 --table_size=1000000 oltp_read_only --db-ps-mode=disable run

[ 1s ] thds: 16 tps: 1162.10 qps: 18716.33 (r/w/o: 16377.17/0.00/2339.17) lat (ms,95%): 23.10 err/s: 0.00 reconn/s: 0.00
[ 2s ] thds: 16 tps: 1314.16 qps: 21020.63 (r/w/o: 18391.30/0.00/2629.33) lat (ms,95%): 18.95 err/s: 0.00 reconn/s: 0.00
[ 30s ] thds: 16 tps: 1119.86 qps: 17924.70 (r/w/o: 15683.99/0.00/2240.71) lat (ms,95%): 21.11 err/s: 0.00 reconn/s: 0.00
SQL statistics:
    queries performed:
        read:                            435092
        write:                           0
        other:                           62156
        total:                           497248
    transactions:                        31078  (1035.24 per sec.)
    queries:                             497248 (16563.84 per sec.)
    ignored errors:                      0      (0.00 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          30.0184s
    total number of events:              31078

Latency (ms):
         min:                                    6.79
         avg:                                   15.45
         max:                                   96.76
         95th percentile:                       27.17
         sum:                               480008.54

Threads fairness:
    events (avg/stddev):           1942.3750/148.44
    execution time (avg/stddev):   30.0005/0.01


# 参数解读

thds: 10，这个意思就是有10个线程在压测
tps: 380.99，这个意思就是每秒执行了380.99个事务
qps: 7610.20，这个意思就是每秒可以执行7610.20个请求
(r/w/o: 5132.99/1155.86/1321.35)，这个意思就是说，在每秒7610.20个请求中，有5132.99个请求是读请求，1155.86个请求是写请求，1321.35个请求是其他的请求，就是对QPS进行了拆解
lat (ms, 95%): 21.33，这个意思就是说，95%的请求的延迟都在21.33毫秒以下
err/s: 0.00 reconn/s: 0.00，这两个的意思就是说，每秒有0个请求是失败的，发生了0次网络重连



SQL statistics:
	queries performed:
		read: 1480084 // 这就是说在300s的压测期间执行了148万多次的读请求
		write: 298457 // 这是说在压测期间执行了29万多次的写请求
		other: 325436 // 这是说在压测期间执行了30万多次的其他请求
		total: 2103977 // 这是说一共执行了210万多次的请求
	// 这是说一共执行了10万多个事务，每秒执行350多个事务
	transactions: 105180( 350.6 per sec. )
	// 这是说一共执行了210万多次的请求，每秒执行7000+请求
	queries: 2103977 ( 7013.26 per sec. )
	ignored errors: 0 (0.00 per sec.)
	reconnects: 0 (0.00 per sec.)
// 下面就是说，一共执行了300s的压测，执行了10万+的事务
General staticstics:
	total time: 300.0052s
	total number of events: 105180
Latency (ms):
	min: 4.32 // 请求中延迟最小的是4.32ms
	avg: 13.42 // 所有请求平均延迟是13.42ms
	max: 45.56 // 延迟最大的请求是45.56ms
	95th percentile: 21.33 // 95%的请求延迟都在21.33ms以内
```


