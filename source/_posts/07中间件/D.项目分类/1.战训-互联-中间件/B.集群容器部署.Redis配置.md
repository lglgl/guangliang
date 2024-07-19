
### 配置文件
```
# ----------------------------------------------------------
#                        Redis配置
# 1、生产环境需修改maxmemory
# 2、端口修改为16379，通信端口会自动+10000
# 3、别配置logfile /logs/redis.log && loglevel warning，docker会起不来
# 4、配置了daemonize yes一样起不了
# ----------------------------------------------------------


# 绑定本机
bind 0.0.0.0
# 修改默认端口
port 16379
# 启用守护进程
daemonize yes
# 指定本地数据库存放目录
dir /data

# 集群配置
cluster-enabled yes
cluster-config-file nodes-16379.conf
cluster-node-timeout 15000
# 打开AOF配置
appendonly yes


# 至少15分钟内有一个key变化；或者5分钟有100个变化；或者60秒有10000个变化，保存一次
save 900 1
save 300 100
save 60 10000

# 设置密码，masterauth从机用，requirepass客户端用
masterauth 'redis#SZT7658'
requirepass 'redis#SZT7658'

# 内存：生产环境设置为64gb
maxmemory 2048mb
```



### 配置防火墙

```shell
firewall-cmd --add-port=6379/tcp --permanent
firewall-cmd --add-port=6380/tcp --permanent
firewall-cmd --add-port=16379/tcp --permanent
firewall-cmd --reload

```





### 启动docker

```shell
#若有docker请先删除
docker stop dev-redis-cluster-01 && docker rm dev-redis-cluster-01
docker stop dev-redis-cluster-02 && docker rm dev-redis-cluster-02
docker stop dev-redis-cluster-03 && docker rm dev-redis-cluster-03
docker stop dev-redis-cluster-04 && docker rm dev-redis-cluster-04
docker stop dev-redis-cluster-05 && docker rm dev-redis-cluster-05
docker stop dev-redis-cluster-06 && docker rm dev-redis-cluster-06
docker stop dev-redis-cluster-07 && docker rm dev-redis-cluster-07
docker stop dev-redis-cluster-08 && docker rm dev-redis-cluster-08

# 创建pod
docker run --restart=always \
--name dev-redis-cluster-8 \
-v /docker_data/redis/conf/redis.conf:/etc/redis/redis.conf \
--net host  \
-d redis:7.0.11 redis-server /etc/redis/redis.conf
-v /docker_data/redis/data3:/data \

```

### 创建集群
删除
rm -rf /data/middleware/redis/data/* && docker-compose down --remove-orphans && docker-compose up -d


```shell
docker exec -it dev-redis-cluster-04 /bin/bash
docker-compose exec prod_cluster_redis_06 bash

redis-cli -h 192.168.10.181 -p 16379 -a redis#SZT7658 cluster nodes

# cluster-replicas 1 表示每个主节点需要1个从节点
redis-cli --cluster create 192.168.10.181:16379 192.168.10.182:16379 192.168.10.183:16379 192.168.10.184:16379 192.168.10.185:16379 192.168.10.186:16379 --cluster-replicas 1 -a redis#SZT7658


```



### 加入集群

```shell
#确认集群信息
root@node1:/data# redis-cli -h 192.168.10.181 -p 16379 -a redis#SZT7658 -c
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
192.168.10.181:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384


192.168.10.181:6379> cluster nodes
e0a18a1f85d514f49983c53ed43f043bcd912e33 192.168.10.184:6379@16379 master - 0 1687140733539 4 connected 12288-16383

#新节点加入集群
redis-cli --cluster add-node 192.168.10.182:6379 192.168.10.181:6379 -a redis@szt2023nomeaning

```




### 问题处理
```shell
WARNING Memory overcommit must be enabled! Without it, a background save or replication may fail under low memory condition. Being disabled, it can can also cause failures without low memory condition, see https://github.com/jemalloc/jemalloc/issues/1328. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.

将vm.overcommit_memory = 1添加到/etc/sysctl.conf中，然后执行sysctl -p生效配置。

WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.

将net.core.somaxconn = 1024添加到/etc/sysctl.conf中，然后执行`sysctl -p`生效配置。


 redis.conf 中设置了 daemonize yes

 当daemonize 设置了yes，表示redis在后台运行，当执行docker-compose执行启动redis进程时，docker发现自己无事可做，容器自动结束，所以导致redis启动失败。



 https://blog.csdn.net/zyxzj/article/details/106905270
```