
### 前置准备
https://prometheus.io/docs/instrumenting/exporters/

```shell
mkdir -p /docker_data/exporter/{node_exporter,mysql,redis,kafka}
```

### Linux探针
```shell
node_exporter-1.5.0.linux-amd64.tar.gz上传至/docker_data/exporter/node_exporter/
cd /docker_data/exporter/node_exporter/
tar -xvf node_exporter-1.5.0.linux-amd64.tar.gz -C ./

cat > /etc/systemd/system/node_exporter.service <<EOF
[Unit]
Description=node_exporeter
After=network.target
[Service]
Type=simple
ExecStart=/docker_data/exporter/node_exporter/node_exporter-1.5.0.linux-amd64/node_exporter
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF


systemctl daemon-reload && systemctl start node_exporter && systemctl enable node_exporter && systemctl status node_exporter
```

验证探针
http://idszt-node001:9100/metrics


### MySQL探针
```shell
cd /docker_data/exporter/mysql/
tar -xvf mysqld_exporter-0.15.0-rc.0.linux-386.tar.gz -C ./
```
创建my.cnf文件(/docker_data/exporter/mysql/)
```shell
[client]
user=root
password=mysql@SZT2021
```


```shell
cat > /etc/systemd/system/mysqld_exporter.service <<EOF
[Unit]
Description=mysqld_exporter
After=network.target
[Service]
Type=simple
ExecStart=/docker_data/exporter/mysql/mysqld_exporter-0.15.0-rc.0.linux-386/mysqld_exporter  --config.my-cnf=/docker_data/exporter/mysql/mysqld_exporter-0.15.0-rc.0.linux-386/my.cnf
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF



systemctl daemon-reload && systemctl start mysqld_exporter && systemctl enable mysqld_exporter && systemctl status mysqld_exporter


```
验证链接：http://192.168.10.185:9104/metrics





### Redis探针
https://github.com/oliver006/redis_exporter/releases/tag/v1.51.0
压缩包上传至/docker_data/exporter/redis/
cd /docker_data/exporter/redis/
tar -xvf redis_exporter-v1.51.0.linux-amd64.tar.gz  -C ./

```shell
cat > /etc/systemd/system/redis_exporter.service <<EOF
[Unit]
Description=redis_exporter
After=network.target
[Service]
Type=simple
ExecStart=/data/exporter/redis_exporter-v1.51.0.linux-amd64/redis_exporter -redis.addr localhost:16379 -redis.password redis#SZT7658
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload && systemctl start redis_exporter && systemctl enable redis_exporter && systemctl status redis_exporter
systemctl daemon-reload && systemctl restart redis_exporter

```


### Kafka探针
mkdir -p /docker_data/exporter/kafka
cd /docker_data/exporter/kafka

tar -xvf kafka_exporter-1.7.0.linux-amd64.tar.gz  -C ./
cd kafka_exporter-1.7.0.linux-amd64/
./kafka_exporter --kafka.server=127.0.0.1:9092
```shell
cat > /etc/systemd/system/kafka_exporter.service <<EOF
[Unit]
Description=kafka_exporter
After=network.target
[Service]
Type=simple
ExecStart=/docker_data/exporter/kafka/kafka_exporter-1.7.0.linux-amd64/kafka_exporter  --kafka.server=127.0.0.1:9092
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload && systemctl start kafka_exporter && systemctl enable kafka_exporter && systemctl status kafka_exporter


```

### Zookeeper探针(放弃内置探针，采用--)
zookeeper内置探针，只需将以下加入配置文件。
配置文件位置：/docker_data/zookeeper/conf/

```shell
metricsProvider.className=org.apache.zookeeper.metrics.prometheus.PrometheusMetricsProvider
metricsProvider.httpPort=7000
metricsProvider.exportJvmInfo=true

```
docker restart prod_zookeeper_cluster_01
docker logs prod_zookeeper_cluster_01


# 文件已下发，执行
systemctl daemon-reload && systemctl start zookeeper_exporter  && systemctl status zookeeper_exporter && systemctl enable zookeeper_exporter


### Elasticsearch
```shell
https://github.com/prometheus-community/elasticsearch_exporter

mkdir -p /docker_data/exporter/elasticsearch/ && cd  /docker_data/exporter/elasticsearch/
tar -xvf elasticsearch_exporter-1.5.0.linux-amd64.tar.gz  -C ./

密码含特殊字符，需转义%40%23

```shell
cat > /etc/systemd/system/elasticsearch_exporter.service <<EOF
[Unit]
Description=elasticsearch_exporter
After=network.target
[Service]
Type=simple
ExecStart=/docker_data/exporter/elasticsearch/elasticsearch_exporter-1.5.0.linux-amd64/elasticsearch_exporter --es.uri http://elastic:szt%40%23es@127.0.0.1:9200 --es.all
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload && systemctl start elasticsearch_exporter && systemctl enable elasticsearch_exporter && systemctl status elasticsearch_exporter
```
验证链接：http://192.168.10.181:9114/metrics



### Minio探针（内置）
参考官网：https://min.io/docs/minio/linux/operations/monitoring/collect-minio-metrics-using-prometheus.html?ref=docs-redirect

参数`MINIO_PROMETHEUS_AUTH_TYPE=public`公开
```shell
docker run -d --name prod_minio_cluster_01 \
--restart=always --net=host \
-e "MINIO_ROOT_USER=minioadmin" \
-e "MINIO_ROOT_PASSWORD=minioadmin" \
-e "MINIO_PROMETHEUS_AUTH_TYPE=public" \
-v /docker_data/minio/data:/data \
-v /docker_data/minio/config:/root/.minio \
 minio/minio:RELEASE.2023-05-04T21-44-30Z server \
 --address 192.168.10.181:9000 http://192.168.10.18{1...6}/data
```


### JMX探针
https://github.com/prometheus/jmx_exporter



### Nacos探针（内置）

添加配置
```shell
docker exec -it prod_nacos_cluster_01 /bin/bash
vi conf/application.properties

management.endpoints.web.exposure.include=*

docker restart prod_nacos_cluster_01
```