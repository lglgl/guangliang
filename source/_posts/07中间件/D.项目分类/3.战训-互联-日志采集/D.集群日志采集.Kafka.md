

### 前置说明

1、先安装zookeeper
2、再安装kafka


### 创建配置数据存放目录
```
mkdir -p /docker_data/loganalysis_zookeeper/{conf,data,datalog}
conf：zoo.cfg
data：myid
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

server.6=192.168.10.186:2888:3888
server.7=192.168.10.187:2888:3888
server.8=192.168.10.188:2888:3888

```

### 创建POD
```
# /etc/localtime为系统时区，无需修改挂在路径
docker run --name prod_loganalysis_zookeeper_02 \
-v /docker_data/loganalysis_zookeeper/conf:/conf \
-v /docker_data/loganalysis_zookeeper/data:/data \
-v /docker_data/loganalysis_zookeeper/datalog:/datalog \
-v /etc/localtime:/etc/localtime \
--net=host \
--restart=always \
--privileged=true \
-d zookeeper:3.7.1
```



### 查看集群
正常情况下node3应该成为leader



```shell
mkdir -p /docker_data/loganalysis_kafka/{config,data,plugins,logs}

docker stop prod_kafka_cluster_01 && docker rm prod_kafka_cluster_01
docker logs prod_kafka_cluster_02
```


需修改三处：name，KAFKA_BROKER_ID，KAFKA_ADVERTISED_LISTENERS

```shell
docker run --name prod_loganalysis_kafka_03 \
-e KAFKA_BROKER_ID=3 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.10.186:9092 \
-e KAFKA_ZOOKEEPER_CONNECT=192.168.10.186:2181,192.168.10.187:2181,192.168.10.188:2181 \
-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 \
-e KAFKA_HEAP_OPTS="-Xmx1024m -Xms1024m" \
-e TZ='Asia/Shanghai' \
-e LANG="en_US.UTF-8" \
-v /docker_data/loganalysis_kafka:/kafka \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /etc/localtime:/etc/localtime \
--network=host --privileged=true --restart=always \
-d wurstmeister/kafka:2.13-2.8.1
```

验证集群状态：
```shell
docker exec -it prod_loganalysis_zookeeper_02 /bin/bash
zkServer.sh status
```