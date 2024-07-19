

### 创建配置数据存放目录
```
mkdir -p /docker_data/zookeeper/{conf,data,datalog}
conf：zoo.cfg
data：myid
```

```
# /etc/localtime为系统时区，无需修改挂在路径
docker run --name prod_zookeeper_cluster_09 \
-v /docker_data/zookeeper/conf:/conf \
-v /docker_data/zookeeper/data:/data \
-v /docker_data/zookeeper/datalog:/datalog \
-v /etc/localtime:/etc/localtime \
--net=host \
--restart=always \
--privileged=true \
-d zookeeper:3.7.1
```

### 修改集群配置文件
每台都一样
zoo.cfg
```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/data
clientPort=2181


server.1=192.168.10.181:2888:3888
server.2=192.168.10.182:2888:3888
server.3=192.168.10.183:2888:3888
server.4=192.168.10.184:2888:3888
server.5=192.168.10.185:2888:3888
server.6=192.168.10.186:2888:3888
```

### 查看集群
正常情况下node3应该成为leader

docker exec -it prod_zookeeper_cluster_01 /bin/bash
zkServer.sh status
docker exec -it prod_cluster_zookeeper_01 zkServer.sh status



docker run --name prod_zookeeper_cluster_06 \
-v /data/middleware/zookeeper/config:/conf \
-v /data/middleware/zookeeper/data:/data \
-v /data/middleware/zookeeper/datalog:/datalog \
-v /etc/localtime:/etc/localtime \
-e ZOO_LOG4J_PROP = "INFO,ROLLINGFILE" \
--net=host \
--restart=always \
--privileged=true \
-d zookeeper:3.7.1

 -e ZOO_MY_ID = 4


docker run --name prod_zookeeper_cluster_06 \
-v /data/middleware/zookeeper/config:/conf \
-v /data/middleware/zookeeper/data:/data \
-v /data/middleware/zookeeper/datalog:/datalog \
-v /etc/localtime:/etc/localtime \
-e ZOO_LOG4J_PROP="INFO,ROLLINGFILE" \
-e ZOO_MY_ID=1 \
--net=host \
--restart=always  \
--privileged=true  zookeeper:3.7.1

docker stop  prod_zookeeper_cluster_06 && docker rm  prod_zookeeper_cluster_06
docker exec -it prod_zookeeper_cluster_06 /bin/bash

docker start prod_zookeeper_cluster_06

docker-compose exec prod_cluster_zookeeper_01 bash