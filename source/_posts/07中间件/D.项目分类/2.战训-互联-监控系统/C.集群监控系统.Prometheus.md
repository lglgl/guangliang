
### 安装prometheus
创建配置目录
```
mkdir -p /docker_data/prometheus/
# 创建/docker_data/prometheus/prometheus.yml和rule.yml文件
```

运行
docker stop prod_monitor_prometheus_02 && docker rm prod_monitor_prometheus_02
- `--web.enable-lifecycle`热加载配置

```shell
# 配置路径：/root/docker-prometheus/
chmod 777 /docker_data/prometheus


docker run --name prod_monitor_prometheus_02 --privileged=true -d -p 9090:9090 -v /docker_data/prometheus/:/prometheus/ prom/prometheus --web.enable-lifecycle --storage.tsdb.retention=180d
```

验证链接：http://192.168.10.188:9090/targets?search=





### 安装grafana
https://grafana.com/docs/versions/?project=/docs/grafana/

mkdir -p /docker_data/grafana/plugins
chmod 472 /docker_data/grafana
docker run --name prod_grafana_main_01 -d -p 3000:3000 --web.enable-lifecycle --privileged=true -v /docker_data/grafana:/var/lib/grafana  grafana/grafana

"GF_PATHS_CONFIG=/etc/grafana/grafana.ini",
"GF_PATHS_DATA=/var/lib/grafana",
"GF_PATHS_HOME=/usr/share/grafana",
"GF_PATHS_LOGS=/var/log/grafana",
"GF_PATHS_PLUGINS=/var/lib/grafana/plugins",
"GF_PATHS_PROVISIONING=/etc/grafana/provisioning"