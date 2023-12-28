+++
title = '5.2.Docker环境部署.md'
date = 2023-12-27T02:11:48+08:00
draft = false
+++






```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

### 安装docker

```shell
yum install docker-ce docker-ce-cli containerd.io
//启动docker
systemctl enable docker && systemctl start docker
//查看docker版本
docker --version
//查看是否启动成功
 rpm -qa | grep  docker
```

### 配置国内源（请珍惜生命）
```shell
#docker 设置国内镜像源.创建或修改`vi /etc/docker/daemon.json` 文件，修改为如下形式

{
  "registry-mirrors": [
    "https://4sjn347i.mirror.aliyuncs.com",
    "https://docker.mirrors.ustc.edu.cn"
  ],
  "max-concurrent-downloads": 10,
  "insecure-registries": [],
  "debug": true,
  "experimental": false,
  "features": {
    "buildkit": true
  }
}

```

### docker维护

```shell
#查看运行中的容器
[root@ansible prometheus]# docker ps -a
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                      NAMES
1e67cc0cd48a   prom/prometheus   "/bin/prometheus --c…"   3 minutes ago    Created                                    crazy_varahamihira
121e8c4fae84   prom/prometheus   "/bin/prometheus --c…"   19 minutes ago   Up 19 minutes   127.0.0.1:9090->9090/tcp   prometheus

#停止运行中的容器
[root@ansible prometheus]# docker stop prometheus
prometheus



docker info                # 查看docker的信息

docker search img_name     # 搜索名为img_name的镜像
docker pull img_name       # 将名为img_name的镜像下载到本地
docker images              # 查看本地已有的镜像
docker rmi img_name        # 删除名为img_name的镜像
 
docker ps                  # 列出正在运行的容器
docker ps -a               # 列出所有的容器
# 使用img_name以交互模式在后台运行分配了伪终端的名为container_name的镜像
docker run -itd --name=container_name img_name  
 
docker start container_name/container_id        # 通过容器名字或ID启动容器
docker stop container_name/container_id         # 通过容器名字或ID停止容器
docker restart container_name/container_id      # 通过容器名字或ID重启容器
docker rm container_name/container_id           # 通过容器名字或ID删除容器
 
docker exec -it  id /bin/bash   # 通过容器名字或ID进入容器
exit               # 退出容器

#docker进入容器命令
docker exec -it 44fc0f0582d9 /bin/bash


```



### 查看报错

```shell
#查看log
docker logs 34d9cdf91e46


1. # 通过查看容器日志，确定是什么问题,docker logs -f -t --tail 行数 容器名
    docker logs -f -t --tail 100 <containerID> 
2. # 将容器中导致错误的文件拷贝到宿主机上
    docker cp containerID:container_path host_path 
    ***说明***
    	containerID：容器ID
    	container_path：容器内文件路径（需拷贝的源文件）
    	host_path：宿主机路径（拷贝文件的目标）
3. # 修改拷贝出来的配置文件, 再将配置文件拷贝回去容器 
    docker cp host_path containerID:container_path
4. # 再次启动容器
    docker start containerID

```




