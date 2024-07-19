## Docker

Linux离线下载包

```shell
yum -y install docker-ce docker-ce-cli containerd.io --downloadonly --downloaddir=/root/software

## 安装docker-compose
yum install -y epel-release && yum install -y docker-compose --downloadonly --downloaddir=/root/software
[root@idszt-node002 software]# docker-compose --version
docker-compose version 1.18.0, build 8dd22a9



[root@localhost ~]# cd software/
[root@localhost software]# yum localinstall *.rpm -y
[root@localhost software]# docker --version
Docker version 24.0.2, build cb74dfc

systemctl enable docker.service
systemctl start docker.service
```

## 微服务中间件
部署之前请核对开发环境版本
```shell
redis               7.0.11     0ec8ab59a35f       117MB
zookeeper           3.7.1      2665a3b2800b       273MB
wurstmeister/kafka  2.13-2.8.1 2dd91ce2efe1       508MB
nacos/nacos-server  v2.1.1     7c38e05f6228       1.06GB
minio/minio         RELEASE.2023-05-04T21-44-30Z   3067751629ce    257MB
docker.elastic.co/elasticsearch/elasticsearch   7.15.2     9cd5b752c86e      791MB
```

### tag标签
docker save d4cdde7ce77b > /root/docker_image/grafana-10.0.2.tar
docker save e1fbd49323c6 > /root/docker_image/prometheus-v2.45.0.tar
docker save c8568f914cd2 > /root/docker_image/alertmanager-v0.25.0.tar

docker pull fauria/vsftpd:latest
docker pull nginx:1.25
docker save 021283c8eb95 > /root/docker_image/nginx-1.25.tar
docker save 9bfb39139661 > /root/docker_image/vsftpd-3.0.tar

```shell
docker load < redis-7.0.11.tar
docker tag 0ec8ab59a35f redis:7.0.11

docker load < zookeeper-3.7.1.tar
docker tag 2665a3b2800b zookeeper:3.7.1


docker load < kafka-2.13_2.8.1.tar
docker tag 2dd91ce2efe1 wurstmeister/kafka:2.13-2.8.1

docker load < nacos-server-v2.1.1.tar
docker tag 7c38e05f6228 nacos/nacos-server:v2.1.1

docker load < minio-RELEASE.2023-05-04T21-44-30Z.tar
docker tag 3067751629ce minio/minio:RELEASE.2023-05-04T21-44-30Z

docker load < elasticsearch-7.15.2.tar
docker tag 9cd5b752c86e docker.elastic.co/elasticsearch/elasticsearch:7.15.2

docker load < nginx-1.25.tar
docker tag 021283c8eb95 nginx:1.25

docker load < vsftpd-3.0.tar
docker tag 9bfb39139661 fauria/vsftpd


```

```shell



docker images

[root@idszt-node007 docker_mirror]# docker images
REPOSITORY                                      TAG                            IMAGE ID       CREATED         SIZE
redis                                           7.0.11                         0ec8ab59a35f   7 weeks ago     117MB
minio/minio                                     RELEASE.2023-05-04T21-44-30Z   3067751629ce   2 months ago    257MB
nacos/nacos-server                              v2.1.1                         7c38e05f6228   7 months ago    1.06GB
zookeeper                                       3.7.1                          2665a3b2800b   11 months ago   273MB
wurstmeister/kafka                              2.13-2.8.1                     2dd91ce2efe1   18 months ago   508MB
docker.elastic.co/elasticsearch/elasticsearch   7.15.2                         9cd5b752c86e   20 months ago   791MB

```




