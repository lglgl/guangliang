### 配置持久化目录
```
#确认没有data
rm -rf /docker_data/redis/data/
mkdir -p /docker_data/redis/data3

mkdir -p /docker_data/redis/{conf,data}
cd  /docker_data/redis/conf/
vi redis.conf
#粘贴以下配置文件

```

### 配置文件
```
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes-6379.conf
cluster-node-timeout 15000
appendonly yes
# in product enviroment maxmemory set 64gb
maxmemory 400mb

# when 10 keys changed, take a snapshotting every 10 mins
save 600 1

# set the passward
masterauth 'redis@szt2023nomeaning'
requirepass 'redis@szt2023nomeaning'
```



### 配置防火墙

```
firewall-cmd --add-port=6379/tcp --permanent
firewall-cmd --add-port=6380/tcp --permanent
firewall-cmd --add-port=16379/tcp --permanent
firewall-cmd --reload

```





### 启动docker

```
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

```
docker exec -it dev-redis-cluster-04 /bin/bash


redis-cli -h 192.168.10.181 -p 6379 -a redis@szt2023nomeaning cluster nodes


redis-cli --cluster create 192.168.10.181:6379 192.168.10.182:6379 192.168.10.183:6379 192.168.10.184:6379 192.168.10.185:6379 192.168.10.186:6379 192.168.10.187:6379 192.168.10.188:6379 --cluster-replicas 1 -a redis@szt2023nomeaning


```



### 加入集群

```
#确认集群信息
root@node1:/data# redis-cli -h 192.168.10.181 -p 6379 -a redis@szt2023nomeaning -c
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
192.168.10.181:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384


192.168.10.181:6379> cluster nodes
e0a18a1f85d514f49983c53ed43f043bcd912e33 192.168.10.184:6379@16379 master - 0 1687140733539 4 connected 12288-16383

#新节点加入集群
redis-cli --cluster add-node 192.168.10.182:6379 192.168.10.181:6379 -a redis@szt2023nomeaning

```

