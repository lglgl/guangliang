### 版本
docker pull fauria/vsftpd:latest
docker pull nginx:1.25
docker save 021283c8eb95 > /root/docker_image/nginx-1.25.tar
docker save 9bfb39139661 > /root/docker_image/vsftpd-3.0.tar


cd /data/program/docker_mirror
docker load < nginx-1.25.tar
docker tag 021283c8eb95 nginx:1.25

docker load < vsftpd-3.0.tar
docker tag 9bfb39139661 fauria/vsftpd:3.0

docker load < mysql-8.0.32.tar
docker tag 412b8cc72e4a mysql:8.0.32



### MySQL
docker内部
配置：/etc/my.cnf
数据：/var/lib/mysql
日志：/var/log/mysql


### vsftpd
docker exec -it vsftpd_prod_papsystem_vsftpd_01_1 /bin/bash
日志：/var/log/vsftpd/
文件：/home/vsftpd



### nginx
docker exec -it nginx_prod_papsystem_nginx_01_1 /bin/bash

docker cp nginx_prod_papsystem_nginx_01_1:/etc/nginx /root/nginx


firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.10.66" port protocol="tcp" port="443" accept'

sudo firewall-cmd --zone=public --permanent --add-port=80/tcp
sudo firewall-cmd --zone=public --permanent --add-port=443/tcp
sudo firewall-cmd --zone=public --permanent --add-port=444/tcp
firewall-cmd --reload





