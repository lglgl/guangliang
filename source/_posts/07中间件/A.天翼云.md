59.36.150.13


192.168.0.32

[root@tyy-manager ~]# cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core)


[root@gf-node001 ~]# cat /etc/redhat-release
CentOS Linux release 8.2.2004 (Core)


[root@gf-node002 ~]# cat /etc/redhat-release
CentOS Linux release 8.2.2004 (Core)


mkfs -t xfs /dev/vdb

### 分区添加磁盘

```shell
[root@gf-node002 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        7.8G     0  7.8G   0% /dev
tmpfs           7.8G     0  7.8G   0% /dev/shm
tmpfs           7.8G  8.6M  7.8G   1% /run
tmpfs           7.8G     0  7.8G   0% /sys/fs/cgroup
/dev/vda2        36G  3.2G   31G  10% /
tmpfs           1.6G     0  1.6G   0% /run/user/0
[root@gf-node002 ~]# fdisk /dev/vdb

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x30032b50.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-419430399, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-419430399, default 419430399):

Created a new partition 1 of type 'Linux' and of size 200 GiB.

Command (m for help): p
Disk /dev/vdb: 200 GiB, 214748364800 bytes, 419430400 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x30032b50

Device     Boot Start       End   Sectors  Size Id Type
/dev/vdb1        2048 419430399 419428352  200G 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

[root@gf-node002 ~]# mkfs -t ext4 /dev/vdb
mke2fs 1.45.4 (23-Sep-2019)
Found a dos partition table in /dev/vdb
Proceed anyway? (y,N) y
Creating filesystem with 52428800 4k blocks and 13107200 inodes
Filesystem UUID: 72705ecc-393a-48b0-8a44-d81d177645b4
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872

Allocating group tables: done
Writing inode tables: done
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done

[root@gf-node002 ~]# mkdir /data
[root@gf-node002 ~]# mount /dev/vdb /data
[root@gf-node002 ~]# df -lh
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        7.8G     0  7.8G   0% /dev
tmpfs           7.8G     0  7.8G   0% /dev/shm
tmpfs           7.8G  8.6M  7.8G   1% /run
tmpfs           7.8G     0  7.8G   0% /sys/fs/cgroup
/dev/vda2        36G  3.2G   31G  10% /
tmpfs           1.6G     0  1.6G   0% /run/user/0
/dev/vdb        196G   61M  186G   1% /data



```


### 安装nginx

```shell
yum -y install yum-utils --downloadonly --downloaddir=/root/software

yum -y install nginx --downloadonly --downloaddir=/root/software

cd /root/software

yum localinstall *.rpm -y

nginx -v


[root@gf-node002 software]# nginx -v
nginx version: nginx/1.24.0
[root@gf-node002 software]# whereis nginx
nginx: /usr/sbin/nginx /usr/lib64/nginx /etc/nginx /usr/share/nginx /usr/share/man/man8/nginx.8.gz
#启动nginx。
systemctl start nginx
#开机启动nginx
systemctl enable nginx




```