### 前置说明
nacos数据保存在MySQL，故直接配置运行即可。无单独data和config目录。


### 下载数据库脚本

```
https://raw.githubusercontent.com/alibaba/nacos/develop/distribution/conf/mysql-schema.sql

utf8mb4/utf8mb4_bin
```

docker stop prod_nacos_cluster_01 && docker rm prod_nacos_cluster_01
### 配置说明
https://github.com/nacos-group/nacos-docker

修改集群NACOS_SERVERS，MYSQL_SERVICE_HOST，NACOS_SERVER_IP（有变动，使用node006配置）
```shell

docker-compose exec prod_cluster_nacos_01 bash

docker run -d \
-e MODE=cluster \
-e NACOS_APPLICATION_PORT=8848 \
-e NACOS_SERVERS="192.168.10.181:8848 192.168.10.182:8848 192.168.10.183:8848 192.168.10.184:8848 192.168.10.185:8848 192.168.10.186:8848" \
-e JVM_XMS=4g -e JVM_XMX=4g JVM_XMN=2g \

-e SPRING_DATASOURCE_PLATFORM=mysql \
-e MYSQL_SERVICE_HOST=192.168.10.186 \
-e MYSQL_SERVICE_PORT=3306 \
-e MYSQL_SERVICE_USER=root \
-e MYSQL_SERVICE_PASSWORD=mysql@SZT2021 \
-e MYSQL_SERVICE_DB_NAME=dev_nacos \
-e NACOS_SERVER_IP=192.168.10.181 \
--network=host --privileged=true --restart=always \
--name prod_nacos_cluster_01 \
nacos/nacos-server:v2.1.1


docker run -d \
-e MODE=cluster \
-e NACOS_APPLICATION_PORT=8848 \
-e NACOS_SERVERS="192.168.10.181:8848 192.168.10.182:8848 192.168.10.183:8848 192.168.10.184:8848 192.168.10.185:8848 192.168.10.186:8848" \
-e SPRING_DATASOURCE_PLATFORM=mysql \
-e MYSQL_SERVICE_HOST=192.168.10.186 \
-e MYSQL_SERVICE_PORT=3306 \
-e MYSQL_SERVICE_USER=root \
-e MYSQL_SERVICE_PASSWORD=mysql@SZT2021 \
-e MYSQL_SERVICE_DB_NAME=dev_nacos \
-e NACOS_SERVER_IP=192.168.10.186 \
--network=host --privileged=true --restart=always \
--name prod_nacos_cluster_06 \
nacos/nacos-server:v2.1.1

```





### 验证集群
登录任意节点：http://192.168.10.186:8848/nacos/#/login（nacos/nacos）

