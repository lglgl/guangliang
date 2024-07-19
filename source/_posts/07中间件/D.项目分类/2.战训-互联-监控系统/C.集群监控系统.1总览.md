### 版本
docker pull prom/prometheus:v2.45.0
docker pull prom/alertmanager:v0.25.0
docker pull grafana/grafana:10.0.2


cd /data/program/docker_mirror/
docker load < prometheus-v2.45.0.tar && docker tag e1fbd49323c6 prom/prometheus:v2.45.0

docker load < alertmanager-v0.25.0.tar && docker tag c8568f914cd2 prom/alertmanager:v0.25.0

docker load < grafana-10.0.2.tar && docker tag d4cdde7ce77b grafana/grafana:10.0.2

docker image ls


docker cp da1a4fcb50cf:/etc/grafana/grafana.ini /data/grafana.ini



### 查看开发程序侦听的端口
netstat -lnpt |grep java