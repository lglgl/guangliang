### 数据文件夹
data
  middleware
    elasticsearch
      config
      data
      plugins
      logs
    kafka
    minio
      data
      config
    nacos
    redis
      config/redis.conf
      data
    zookeeper
      conf
      data
      datalog
  loganalysis
    filebeat
    grafana
    kafka
    kibana
    logstash
    elasticsearch
  mhprogram（开发程序）
  monitor
    exporter
    prometheus
    grafana
    alertmanager

home/idszt
  docker-compose
    node001
    node002
    node003
    node004
    node005
    node006
    node007
    node008
  system-maintain
    ansible

mkdir  node001 \
    node002 \
    node003\
    node004\
    node005\
    node006\
    node007\
    node008\
    node009


磁盘分区
```shell
cat /etc/fstab
/dev/sdb1 /data                                  xfs     defaults        0 0

echo '/dev/sdb1 /data                                  xfs     defaults        0 0' >> /etc/fstab



[root@local-node002 docker-compose]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0  100G  0 disk
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   99G  0 part
  ├─centos-root 253:0    0   50G  0 lvm  /
  ├─centos-swap 253:1    0  7.9G  0 lvm  [SWAP]
  └─centos-home 253:2    0 41.1G  0 lvm  /home
sdb               8:16   0  120G  0 disk
sr0              11:0    1  9.6G  0 rom
[root@local-node002 docker-compose]# fdisk -l

磁盘 /dev/sdb：128.8 GB, 128849018880 字节，251658240 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节


磁盘 /dev/sda：107.4 GB, 107374182400 字节，209715200 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x000a7729

   设备 Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200   209715199   103808000   8e  Linux LVM

磁盘 /dev/mapper/centos-root：53.7 GB, 53687091200 字节，104857600 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节


磁盘 /dev/mapper/centos-swap：8455 MB, 8455716864 字节，16515072 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节


磁盘 /dev/mapper/centos-home：44.1 GB, 44149243904 字节，86228992 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节

[root@local-node002 docker-compose]# fdisk /dev/sdb
欢迎使用 fdisk (util-linux 2.23.2)。

更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。

Device does not contain a recognized partition table
使用磁盘标识符 0xb67d57e8 创建新的 DOS 磁盘标签。

命令(输入 m 获取帮助)：n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
分区号 (1-4，默认 1)：
起始 扇区 (2048-251658239，默认为 2048)：
将使用默认值 2048
Last 扇区, +扇区 or +size{K,M,G} (2048-251658239，默认为 251658239)：
将使用默认值 251658239
分区 1 已设置为 Linux 类型，大小设为 120 GiB

命令(输入 m 获取帮助)：wq
The partition table has been altered!

Calling ioctl() to re-read partition table.
正在同步磁盘。
[root@local-node002 docker-compose]# mkfs -t xfs /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=7864256 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=31457024, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=15359, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@local-node002 docker-compose]# mount /dev/sdb1 /data
[root@local-node002 docker-compose]# df -lh
文件系统                 容量  已用  可用 已用% 挂载点
devtmpfs                 2.9G     0  2.9G    0% /dev
tmpfs                    2.9G     0  2.9G    0% /dev/shm
tmpfs                    2.9G  282M  2.6G   10% /run
tmpfs                    2.9G     0  2.9G    0% /sys/fs/cgroup
/dev/mapper/centos-root   50G   15G   36G   29% /
/dev/mapper/centos-home   42G   37M   42G    1% /home
/dev/sda1               1014M  187M  828M   19% /boot
tmpfs                    579M   12K  579M    1% /run/user/42
tmpfs                    579M     0  579M    0% /run/user/0
/dev/sdb1                120G   33M  120G    1% /data
[root@local-node002 docker-compose]# echo '/dev/sdb1 /data                                  xfs     defaults        0 0' >> /etc/fstab

```


ifconfig ens33:0 192.168.139.18 netmask 255.255.255.0 up


### 时间同步
ntpdate 119.23.155.163


time dd if=/dev/zero-iotest of=test.file bs=8G count=4 oflag=direct


### 系统配置
vi /etc/sysctl.conf

```shell
# redis配置
vm.max_map_count=262144
net.core.somaxconn = 1024

```


### 添加变量短路径(后期配)
/etc/profile
```shell
export kafka_docker-compose_path=/home/idszt/docker-compose/middleware/kafka


```

### 关闭交换空间
# 注释 swap 行
vim /etc/fstab
```shell

sed -i "s/^[^#].*centos-swap*/#&/g" /etc/fstab
#： s:替换
#:  ^:开头匹配
#： [^#]:匹配非#含centos-swap的行
#： #&:中的&代表匹配整行，整个意思就是行前面加上#号
#： g:全部（只匹配特定行不加）
```

```shell
sed -i "s/^[^#].*centos-swap*/#&/g" /etc/fstab && cat /etc/fstab
[root@idszt-node001 kafka]# cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Sun Jun  4 15:21:48 2023
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=36bd4756-1521-4eea-93cb-9c8125b81e74 /boot                   xfs     defaults        0 0
UUID=B8B9-3229          /boot/efi               vfat    umask=0077,shortname=winnt 0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
#/dev/mapper/centos-swap swap                    swap    defaults        0 0
/dev/sdb1 /data                                 ext4    defaults        0 0

```

apm_system
kibana_system
logstash_system
beats_system

113.98.244.167
113.98.244.169
113.98.244.170
113.98.244.181
113.98.244.183
113.98.244.184
113.98.244.185
113.98.244.187
