
````shell
mkdir -p /docker_data/kafka/{config,data,plugins,logs}

docker stop prod_kafka_cluster_01 && docker rm prod_kafka_cluster_01
docker logs prod_kafka_cluster_02
```


需修改三处：name，KAFKA_BROKER_ID，KAFKA_ADVERTISED_LISTENERS

```shell
docker run --name prod_kafka_cluster_01 \
-e KAFKA_BROKER_ID=1 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.10.181:9092 \
-e KAFKA_ZOOKEEPER_CONNECT=192.168.10.181:2181,192.168.10.182:2181,192.168.10.183:2181,192.168.10.184:2181,192.168.10.185:2181,192.168.10.186:2181 \
-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 \
-e KAFKA_HEAP_OPTS="-Xmx1024m -Xms1024m" \
-e TZ='Asia/Shanghai' \
-e LANG="en_US.UTF-8" \
-v /docker_data/kafka:/kafka \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /etc/localtime:/etc/localtime \
--network=host --privileged=true --restart=always \
-d wurstmeister/kafka:2.13-2.8.1
```