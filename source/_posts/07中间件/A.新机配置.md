### 安装docker
```shell
cd /data/program/docker_install
yum localinstall *.rpm -y

已安装:
  container-selinux.noarch 2:2.119.2-1.911c772.el7_8
  containerd.io.x86_64 0:1.6.21-3.1.el7
  docker-buildx-plugin.x86_64 0:0.10.5-1.el7
  docker-ce.x86_64 3:24.0.2-1.el7
  docker-ce-cli.x86_64 1:24.0.2-1.el7
  docker-ce-rootless-extras.x86_64 0:24.0.2-1.el7
  docker-compose-plugin.x86_64 0:2.18.1-1.el7
  fuse-overlayfs.x86_64 0:0.7.2-6.el7_8
  fuse3-libs.x86_64 0:3.6.1-4.el7
  slirp4netns.x86_64 0:0.4.3-4.el7_8
完毕！
# 确认版本
[root@idszt-node102 docker_install]# docker --version
Docker version 24.0.2, build cb74dfc
# 设置启动
systemctl enable docker.service
systemctl start docker.service
```

### 导入镜像

docker pull fauria/vsftpd:latest
docker pull nginx:1.25

docker save 021283c8eb95 > /root/docker_image/nginx-1.25.tar
docker save 9bfb39139661 > /root/docker_image/vsftpd-3.0.tar
docker save 412b8cc72e4a > /root/docker_image/mysql-8.0.32.tar

docker load < nginx-1.25.tar
docker tag 021283c8eb95 nginx:1.25

docker load < vsftpd-3.0.tar
docker tag 9bfb39139661 fauria/vsftpd:3.0

docker load < mysql-8.0.32.tar
docker tag 412b8cc72e4a mysql:8.0.32


ip route add default via 113.98.244.161
route add default gw 113.98.244.161
