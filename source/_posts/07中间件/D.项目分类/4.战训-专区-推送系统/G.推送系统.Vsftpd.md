# 1 进入docker ftp交互模式:
docker exec -i -t ftp bash

-i	连接容器的STDIN和容器外的STDIN
-t	告诉 docker 内部的主进程它的输入是终端设备
ftp	指定要进入的容器的名字（这个是在创建的时候使用–name指定的）
bash	使用bash作为终端

# 2 创建用户家目录并赋予权限:
mkdir /home/vsftpd/idszt_ftp_in && chown -R ftp.ftp /home/vsftpd/idszt_ftp_in
mkdir /home/vsftpd/idszt_ftp_out && chown -R ftp.ftp /home/vsftpd/idszt_ftp_out

# 3 将账号密码信息写入文件(注意：该命令在容器内执行, \n 是换行符)
echo -e "idszt_ftp_in\nftp#SZT9653" >> /etc/vsftpd/virtual_users.txt
echo -e "idszt_ftp_out\nftp#SZT3835" >> /etc/vsftpd/virtual_users.txt

# 4.使用脚本读取刚刚写入的文件(注意：该命令在容器内执行)
/usr/bin/db_load -T -t hash -f /etc/vsftpd/virtual_users.txt /etc/vsftpd/virtual_users.db

# 5.退出容器(注意：该命令在容器内执行)
exit

# 6.重启ftp
docker restart ftp

------------------------------------------------------------------------

pstree -p pid |wc -l 

yum install -y vsftpd
yum install vsftpd --downloadonly --downloaddir=/home/idszt
yum localinstall *.rpm -y
systemctl restart vsftpd.service
systemctl status vsftpd.service


配置路径：/etc/vsftpd/


```shell
# 默认配置
ftpd_banner=Welcome to IDSZT@Node101 FTP!

anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_file=/var/log/xferlog
xferlog_std_format=YES
ascii_upload_enable=YES
ascii_download_enable=YES
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
allow_writeable_chroot=YES
listen=NO
listen_ipv6=YES
pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES

# 自定义
anon_world_readable_only=NO
max_clients=5000
max_per_ip=0
listen_port=10021
anon_upload_enable=YES
pasv_enable=YES
pasv_min_port=50000
pasv_max_port=55000
```

/etc/vsftpd/chroot_list
```shell
idszt
idszt_ftp_in
idszt_ftp_out
```



vim /etc/vsftpd/passwd
```shell
idszt
ftp#SZT3827
idszt_ftp_in
ftp#SZT9653
idszt_ftp_out
ftp#SZT3835
```
ftp#SZT9653
db_load -T -t hash -f   /etc/vsftpd/passwd  /etc/vsftpd/passwd.db





vi /etc/pam.d/vsftpd


```shell
#%PAM-1.0
auth    sufficient      pam_userdb.so db=/etc/vsftpd/passwd.db
account sufficient      pam_userdb.so db=/etc/vsftpd/passwd.db
session    optional     pam_keyinit.so    force revoke
auth       required     pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
#auth       required    pam_shells.so
#auth       include     password-auth
account    include      password-auth
session    required     pam_loginuid.so
session    include      password-auth


```





useradd -d /home/idszt_ftp_in idszt_ftp_in
useradd -d /home/idszt_ftp_out idszt_ftp_out
passwd idszt_ftp_in
passwd idszt_ftp_out


systemctl restart vsftpd.service


## ftp error code is 10600
防火墙放通或服务未启动

## 500 OOPS: vsftpd的解决办法
从2.3.5之后，vsftpd增强了安全检查，如果用户被限定在了其主目录下，则该用户的主目录不能再具有写权限了！如果检查发现还有写权限，就会报该错误。（关联SELinux）

Error EElFTPSError: Control channel transfer error 114690
setenforce 0 
setenforce 1
getenforce


针对 CentOS 的 SELinux 拦截 vsftpd 问题(不关闭SELinux)

[root@idszt-node101 ~]# getsebool -a | grep ftpd
ftpd_anon_write --> off
ftpd_connect_all_unreserved --> off
ftpd_connect_db --> off
ftpd_full_access --> off
ftpd_use_cifs --> off
ftpd_use_fusefs --> off
ftpd_use_nfs --> off
ftpd_use_passive_mode --> off


# setsebool -P ftp_home_dir 1 无需


setsebool -P ftpd_full_access 1
setsebool -P ftpd_use_passive_mode 1
setsebool -P ftpd_connect_all_unreserved 1

[root@idszt-node101 ~]# getsebool -a | grep ftpd
ftpd_anon_write --> off
ftpd_connect_all_unreserved --> off
ftpd_connect_db --> off
ftpd_full_access --> on
ftpd_use_cifs --> off
ftpd_use_fusefs --> off
ftpd_use_nfs --> off
ftpd_use_passive_mode --> on


netstat -utlpn | grep vsftp

### 宿主机部署


```shell
# 默认配置
ftpd_banner=Welcome to IDSZT@Node101 FTP!

anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_file=/var/log/xferlog
xferlog_std_format=YES
ascii_upload_enable=YES
ascii_download_enable=YES
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
allow_writeable_chroot=YES
# 侦听ipv4接口
listen=YES
listen_ipv6=NO
pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES

# 自定义
anon_world_readable_only=NO
max_clients=5000
max_per_ip=0
listen_port=10021
anon_upload_enable=YES
pasv_enable=YES
pasv_min_port=50000
pasv_max_port=55000
```


systemctl restart vsftpd