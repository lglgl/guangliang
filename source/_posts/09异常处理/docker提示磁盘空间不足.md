
---
title: docker提示磁盘空间不足
author: 空空
---

## 简述
1. docker启动异常，发现磁盘空间不足，`/var/lib/docker`目录的使用率过高。
2. 进一步检查后，发现inode已经用尽，这是问题的根本。
3. 通过统计根目录下每个目录的文件数量，确认/data目录中的文件数量异常,
4. 溯源到开发端的一个调度监控插件，频繁写入大量日志记录。
5. 与开发端沟通后，设置定期清理/data目录中的文件，释放了大量inode。
6. 重新启动之前无法启动的Docker容器，恢复正常。



## 1. 尝试启动Docker容器，报错显示磁盘空间不足
```shell
[root@dahouzi-dev ~]# docker start 03fc94c2b9d5
Error response from daemon: mkdir /var/lib/docker/overlay2/2409d571b52865348ec99b52e6d3fdca07a2cca7b4cde2be63b6bb264c6adb58/merged: no space left on device
Error: failed to start containers: 03fc94c2b9d5
```

## 2. 查看磁盘使用情况，发现/var/lib/docker的使用率很高
```shell
[root@dahouzi-dev overlay2]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        99G   72G   23G  76% /
devtmpfs        7.8G     0  7.8G   0% /dev
tmpfs           7.8G  4.0K  7.8G   1% /dev/shm
tmpfs           7.8G  1.7M  7.8G   1% /run
tmpfs           7.8G     0  7.8G   0% /sys/fs/cgroup
tmpfs           1.6G     0  1.6G   0% /run/user/0
overlay          99G   72G   23G  76% /var/lib/docker/overlay2/16cc70551c2811f5821775984a801cdb4f06f825a557edb347862650628f66d5/merged
overlay          99G   72G   23G  76% /var/lib/docker/overlay2/1f9c213147c08ea2f6e7ad953043f4ae96279be391d223d5cedb769fc2f7e6bf/merged
overlay          99G   72G   23G  76% /var/lib/docker/overlay2/b285f624b11407a137448e1c6e74759b52d59b2d049c84ee344247ee7136df05/merged
overlay          99G   72G   23G  76% /var/lib/docker/overlay2/412ab0e2af0495c08968d6ae14d3b6777ba33d15e3d59fe7d1eef5f474e2d81d/merged
overlay          99G   72G   23G  76% /var/lib/docker/overlay2/e4e4f808f6685ff3f525244ea2122c3de054c3ebd828a6c343dd6180a942758e/merged
overlay          99G   72G   23G  76% /var/lib/docker/overlay2/b9a4fa2196a6980a21d59c4d14737e0fe566a82b8ce55c8e5d3a9452cc3d8419/merged

```

## 3. 查看inode使用情况，发现inode已满
已达到系统默认最大值6553600
```shell
[root@dahouzi-dev overlay2]# df -i
Filesystem      Inodes   IUsed   IFree IUse% Mounted on
/dev/vda1      6553600 6553600       0  100% /
devtmpfs       2030601     360 2030241    1% /dev
tmpfs          2033290       3 2033287    1% /dev/shm
tmpfs          2033290     784 2032506    1% /run
tmpfs          2033290      16 2033274    1% /sys/fs/cgroup
tmpfs          2033290       1 2033289    1% /run/user/0
overlay        6553600 6553600       0  100% /var/lib/docker/overlay2/16cc70551c2811f5821775984a801cdb4f06f825a557edb347862650628f66d5/merged
overlay        6553600 6553600       0  100% /var/lib/docker/overlay2/1f9c213147c08ea2f6e7ad953043f4ae96279be391d223d5cedb769fc2f7e6bf/merged
overlay        6553600 6553600       0  100% /var/lib/docker/overlay2/b285f624b11407a137448e1c6e74759b52d59b2d049c84ee344247ee7136df05/merged
overlay        6553600 6553600       0  100% /var/lib/docker/overlay2/412ab0e2af0495c08968d6ae14d3b6777ba33d15e3d59fe7d1eef5f474e2d81d/merged
overlay        6553600 6553600       0  100% /var/lib/docker/overlay2/e4e4f808f6685ff3f525244ea2122c3de054c3ebd828a6c343dd6180a942758e/merged
overlay        6553600 6553600       0  100% /var/lib/docker/overlay2/b9a4fa2196a6980a21d59c4d14737e0fe566a82b8ce55c8e5d3a9452cc3d8419/merged

```

## 4. 查看所有Docker容器的状态
```shell
[root@dahouzi-dev containers]# docker ps -a
CONTAINER ID   IMAGE                                                  COMMAND                  CREATED        STATUS                          PORTS                                                  NAMES
196fe0f44767   minio/minio:RELEASE.2023-05-04T21-44-30Z               "/usr/bin/docker-ent…"   42 hours ago   Exited (0) About a minute ago                                                          dev-minio-standalone-04
03fc94c2b9d5   wurstmeister/kafka:2.13-2.8.1                          "start-kafka.sh"         2 days ago     Exited (1) 11 hours ago                                                                dev-kafka-standalone-01
499cda90bd18   docker.elastic.co/elasticsearch/elasticsearch:7.15.2   "/bin/tini -- /usr/l…"   2 days ago     Up 47 hours                     0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp         dev-es-standalone-01
c4639ca30b87   nacos/nacos-server:v2.1.1                              "bin/docker-startup.…"   2 days ago     Up 2 days                       0.0.0.0:8848->8848/tcp                                 dev-nacos-standalone-01
859f688f4f88   mysql:8.0.32                                           "docker-entrypoint.s…"   2 days ago     Exited (2) 9 hours ago                                                                 dev-mysql-standalone-01
f73314fd956e   docker.elastic.co/kibana/kibana:7.15.2                 "/bin/tini -- /usr/l…"   7 weeks ago    Exited (137) 2 days ago                                                                dev-kibana-standalone-01
edabda1c3642   zookeeper:3.7.1                                        "/docker-entrypoint.…"   7 weeks ago    Up 2 days                       2888/tcp, 3888/tcp, 0.0.0.0:2181->2181/tcp, 8080/tcp   dev-zookeeper-standalone-01
2269262cd40b   redis:7.0.11                                           "docker-entrypoint.s…"   7 weeks ago    Up 2 days                       0.0.0.0:6379->6379/tcp                                 dev-redis-standalone-01
c2c59fff741c   portainer/portainer                                    "/portainer"             5 months ago   Up 2 days                       0.0.0.0:9999->9000/tcp                                 portainer
```

## 5. 统计根目录下每个目录的文件数量
关注`/data 5956444`是容器挂载出来的文件夹，进一步统计或进入查看即可发现原因
```shell
[root@dahouzi-dev overlay2]# for i in /*; do echo $i; find $i | wc -l; done
/bin
1
/boot
332
--------/data 5956444-------
/dev
369
/etc
2469
/ftpdir
146
/ftpdir1
12
/ftpoffline
1
/home
28211
/lib
1
/lib64
1
/lost+found
1
/media
1
/mnt
1
/opt
263923
/proc
294447
/root
3651
/run
649
/sbin
1
/srv
1
/sys
245
/tmp
1
/usr
475924
/var
147674
```

## 6. 重启之前无法启动的容器
```shell
[root@dahouzi-dev ~]# docker start 03fc94c2b9d5
03fc94c2b9d5
```

## 7. 验证容器成功启动
```
[root@dahouzi-dev ~]# docker ps
CONTAINER ID   IMAGE                                                  COMMAND                  CREATED        STATUS              PORTS                                                  NAMES
196fe0f44767   minio/minio:RELEASE.2023-05-04T21-44-30Z               "/usr/bin/docker-ent…"   42 hours ago   Up About a minute                                                          dev-minio-standalone-04
03fc94c2b9d5   wurstmeister/kafka:2.13-2.8.1                          "start-kafka.sh"         2 days ago     Up 2 seconds                                                                dev-kafka-standalone-01
499cda90bd18   docker.elastic.co/elasticsearch/elasticsearch:7.15.2   "/bin/tini -- /usr/l…"   2 days ago     Up 47 hours         0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp         dev-es-standalone-01
c4639ca30b87   nacos/nacos-server:v2.1.1                              "bin/docker-startup.…"   2 days ago     Up 2 days           0.0.0.0:8848->8848/tcp                                 dev-nacos-standalone-01
859f688f4f88   mysql:8.0.32                                           "docker-entrypoint.s…"   2 days ago     Up 1 second                                                                 dev-mysql-standalone-01
f73314fd956e   docker.elastic.co/kibana/kibana:7.15.2                 "/bin/tini -- /usr/l…"   7 weeks ago    Up About a minute                                                          dev-kibana-standalone-01
edabda1c3642   zookeeper:3.7.1                                        "/docker-entrypoint.…"   7 weeks ago    Up 2 days           2888/tcp, 3888/tcp, 0.0.0.0:2181->2181/tcp, 8080/tcp   dev-zookeeper-standalone-01
2269262cd40b   redis:7.0.11                                           "docker-entrypoint.s…"   7 weeks ago    Up 2 days           0.0.0.0:6379->6379/tcp                                 dev-redis-standalone-01
c2c59fff741c   portainer/portainer                                    "/portainer"             5 months ago   Up 2 days           0.0.0.0:9999->9000/tcp                                 portainer
```

