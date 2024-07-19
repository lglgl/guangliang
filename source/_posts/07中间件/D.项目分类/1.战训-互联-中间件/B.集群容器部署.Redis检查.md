### 说明

Redis客户端分单机和集群模式

```shell
redis-cli -h 192.168.240.211 -p 16379 -a redis#SZT7658 -c
```



### 检查集群主从状态
```shell
docker exec -it 7202ff1285d6 /bin/bash
# 或者
docker-compose exec prod_cluster_redis_06 bash

# 进入集群
redis-cli -h 192.168.240.211 -p 16379 -a redis#SZT7658 -c

root@docker-node001:/data# redis-cli -h 192.168.240.211 -p 16379 -a redis#SZT7658 cluster nodes
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
8aafb13878e2ec78c751808882c9605ee03e0716 192.168.240.215:16379@26379 master - 0 1691544277000 7 connected 0-5460
200db90e992c9b9b3f63d56aa562470e500b32e8 192.168.240.213:16379@26379 master - 0 1691544278832 3 connected 10923-16383
69023301118e3f09059f74c38ad261294b0a6d93 192.168.240.211:16379@26379 myself,slave 8aafb13878e2ec78c751808882c9605ee03e0716 0 1691544278000 7 connected
9ce84b0e4447131f45084153882841332f3b89e2 192.168.240.212:16379@26379 master - 0 1691544277828 2 connected 5461-10922
25b34a35e8ecaf25fff0f5ac3e45dfabaa5ab69d 192.168.240.214:16379@26379 slave 200db90e992c9b9b3f63d56aa562470e500b32e8 0 1691544279837 3 connected
ba5c57df2ebf64b38afa06dc70a7a29db13f49cb 192.168.240.216:16379@26379 slave 9ce84b0e4447131f45084153882841332f3b89e2 0 1691544276000 2 connected
```



### key检查

```shell
192.168.240.211:16379> info keyspace
# Keyspace
db0:keys=42,expires=8,avg_ttl=0

# 慎重使用
192.168.240.211:16379> keys *
 1) "merchant_app_info"
 2) "sys_config:succDataTime"


```


### 集群模式获取key值
```shell
# 单机模式进入node212
192.168.240.212:16379> get airport_three_code
"FZX"

# 单机模式进入node213查询失败
192.168.240.213:16379> get key SYSTEM:DICT:CODE_TYPE
(error) ERR wrong number of arguments for 'get' command
192.168.240.213:16379> get airport_three_code
(error) MOVED 7223 192.168.240.212:16379
192.168.240.213:16379> exit

# 集群模式进入redis
root@docker-node003:/data# redis-cli -h 192.168.240.211 -p 16379 -a redis#SZT7658 -c
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
192.168.240.211:16379> get airport_three_code
-> Redirected to slot [7223] located at 192.168.240.212:16379
"FZX"
192.168.240.212:16379> info keyspace
# Keyspace
db0:keys=55,expires=8,avg_ttl=166024718750

```