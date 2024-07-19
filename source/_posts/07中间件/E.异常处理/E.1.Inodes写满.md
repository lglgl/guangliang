

```shell
[root@idszt-dev ~]# docker start 03fc94c2b9d5
Error response from daemon: mkdir /var/lib/docker/overlay2/2409d571b52865348ec99b52e6d3fdca07a2cca7b4cde2be63b6bb264c6adb58/merged: no space left on device
Error: failed to start containers: 03fc94c2b9d5
```



```shell
[root@idszt-dev overlay2]# df -h
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
[root@idszt-dev overlay2]# df -i
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


```shell
[root@idszt-dev containers]# docker ps -a
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

[root@idszt-dev overlay2]# for i in /*; do echo $i; find $i | wc -l; done
/bin
1
/boot
332
/data
5956444
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
30834
/tmp
9
/usr
136673
/var
176741



[root@idszt-dev jobhandler]# pwd
/data/applogs/xxl-job/jobhandler
[root@idszt-dev jobhandler]# for i in /data/applogs/xxl-job/jobhandler/*; do echo $i; find $i |wc -l; done
/data/applogs/xxl-job/jobhandler/2023-06-15
149761
/data/applogs/xxl-job/jobhandler/2023-06-16
149756
/data/applogs/xxl-job/jobhandler/2023-06-17
149761
/data/applogs/xxl-job/jobhandler/2023-06-18
149761
/data/applogs/xxl-job/jobhandler/2023-06-19
149761
/data/applogs/xxl-job/jobhandler/2023-06-20
149761
/data/applogs/xxl-job/jobhandler/2023-06-21
149293
/data/applogs/xxl-job/jobhandler/2023-06-22
149761
/data/applogs/xxl-job/jobhandler/2023-06-23


[root@idszt-dev jobhandler]# ls
2023-06-15  2023-06-18  2023-06-21  2023-06-24  2023-06-27  2023-06-30  2023-07-03  2023-07-08  2023-07-11  2023-07-14  2023-07-17  2023-07-20  2023-07-23  2023-07-26  2023-07-29  2023-08-01  2023-08-04
2023-06-16  2023-06-19  2023-06-22  2023-06-25  2023-06-28  2023-07-01  2023-07-06  2023-07-09  2023-07-12  2023-07-15  2023-07-18  2023-07-21  2023-07-24  2023-07-27  2023-07-30  2023-08-02  callbacklog
2023-06-17  2023-06-20  2023-06-23  2023-06-26  2023-06-29  2023-07-02  2023-07-07  2023-07-10  2023-07-13  2023-07-16  2023-07-19  2023-07-22  2023-07-25  2023-07-28  2023-07-31  2023-08-03  gluesource
[root@idszt-dev jobhandler]# rm -rf 2023-06-[15-30]
[root@idszt-dev jobhandler]# ls
2023-06-15  2023-06-18  2023-06-21  2023-06-24  2023-06-27  2023-06-30  2023-07-03  2023-07-08  2023-07-11  2023-07-14  2023-07-17  2023-07-20  2023-07-23  2023-07-26  2023-07-29  2023-08-01  2023-08-04
2023-06-16  2023-06-19  2023-06-22  2023-06-25  2023-06-28  2023-07-01  2023-07-06  2023-07-09  2023-07-12  2023-07-15  2023-07-18  2023-07-21  2023-07-24  2023-07-27  2023-07-30  2023-08-02  callbacklog
2023-06-17  2023-06-20  2023-06-23  2023-06-26  2023-06-29  2023-07-02  2023-07-07  2023-07-10  2023-07-13  2023-07-16  2023-07-19  2023-07-22  2023-07-25  2023-07-28  2023-07-31  2023-08-03  gluesource
[root@idszt-dev jobhandler]# rm -rf 2023-06-15
[root@idszt-dev jobhandler]# rm -rf 2023-06-18
[root@idszt-dev jobhandler]# df -i
Filesystem      Inodes   IUsed   IFree IUse% Mounted on
/dev/vda1      6553600 6254107  299493   96% /
devtmpfs       2030601     360 2030241    1% /dev
tmpfs          2033290       3 2033287    1% /dev/shm
tmpfs          2033290     649 2032641    1% /run
tmpfs          2033290      16 2033274    1% /sys/fs/cgroup
tmpfs          2033290       1 2033289    1% /run/user/0
[root@idszt-dev jobhandler]# ls 2023-06*
2023-06-16/ 2023-06-17/ 2023-06-19/ 2023-06-20/ 2023-06-21/ 2023-06-22/ 2023-06-23/ 2023-06-24/ 2023-06-25/ 2023-06-26/ 2023-06-27/ 2023-06-28/ 2023-06-29/ 2023-06-30/
[root@idszt-dev jobhandler]# rm -rf 2023-06-**
2023-06-16/ 2023-06-17/ 2023-06-19/ 2023-06-20/ 2023-06-21/ 2023-06-22/ 2023-06-23/ 2023-06-24/ 2023-06-25/ 2023-06-26/ 2023-06-27/ 2023-06-28/ 2023-06-29/ 2023-06-30/
[root@idszt-dev jobhandler]# rm -rf 2023-06-**
[root@idszt-dev jobhandler]# df -i
Filesystem      Inodes   IUsed   IFree IUse% Mounted on
/dev/vda1      6553600 4224352 2329248   65% /
devtmpfs       2030601     360 2030241    1% /dev
tmpfs          2033290       3 2033287    1% /dev/shm
tmpfs          2033290     649 2032641    1% /run
tmpfs          2033290      16 2033274    1% /sys/fs/cgroup
tmpfs          2033290       1 2033289    1% /run/user/0



```