
### 1、安装kibana
docker下两个节点187/188安装，188保留备用不启动。

```shell
# 运行kibana
docker run -d --name prod_loganalysis_kibana_02 \
--network=host --privileged=true --restart=always \
docker.elastic.co/kibana/kibana:7.15.2

# 进入容器内部 调整连接参数
docker exec -it prod_loganalysis_kibana_02 /bin/bash

cat /usr/share/kibana/config/kibana.yml

# 配置文件Default Kibana configuration for docker target
server.host: "0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://192.168.10.186:9200","http://192.168.10.187:9200", "http://192.168.10.188:9200"]
monitoring.ui.container.elasticsearch.enabled: true

i18n.locale: zh-CN
elasticsearch.username: elastic
elasticsearch.password: szt@#es
server.publicBaseUrl: "http://192.168.10.186:5601"
```


- 重启docker：docker restart prod_loganalysis_kibana_02


- 验证：http://192.168.10.188:5601/


-----------------------

