
https://www.elastic.co/guide/en/enterprise-search/7.15/docker.html

### 前置工作
创建外置目录，修改进程可使用最大虚拟内存
```
mkdir -p /docker_data/loganalysis_elasticsearch/{config,data,plugins,logs}
echo "vm.max_map_count=262144" >> /etc/sysctl.conf
cat /etc/sysctl.conf
sysctl -p

#vm.max_map_count = 262144
```
### 创建配置文件
每个节点`/docker_data/loganalysis_elasticsearch/config/elasticsearch.yml`,修改node.name，network.publish_host
```
#配置es的集群名称，默认是elasticsearch，es会自动发现在同一网段下的es，如果在同一网段下有多个集群，就可以用这个属性来区分不同的集群。
cluster.name: prod-loganalysis-es
#节点名称
node.name: loganalysis-es-186
#设置索引数据的存储路径
path.data: ./data
#设置日志的存储路径
path.logs: ./logs
#设置当前的ip地址,通过指定相同网段的其他节点会加入该集群中
network.host: 0.0.0.0
network.publish_host: 192.168.10.186
#设置对外服务的http端口
http.port: 9200
transport.tcp.port: 9300
#设置集群中master节点的初始列表，可以通过这些节点来自动发现新加入集群的节点
#discovery.zen.ping.unicast.hosts: ["0.0.0.0"]
discovery.seed_hosts: ["192.168.10.186:9300","192.168.10.187:9300","192.168.10.188:9300"]
cluster.initial_master_nodes: ["loganalysis-es-186", "loganalysis-es-187","loganalysis-es-188"]

node.master: true
node.data: true

http.cors.enabled: true
http.cors.allow-origin: "*"
```


### 创建实例
```shell
# 记得修改名称和ES_JAVA_OPTS
docker run  --name prod_loganalysis_es_03 \
--network=host --privileged=true --restart=always \
-v /docker_data/loganalysis_elasticsearch/config:/usr/share/elasticsearch/config \
-v /docker_data/loganalysis_elasticsearch/data:/usr/share/elasticsearch/data \
-v /docker_data/loganalysis_elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-v /docker_data/loganalysis_elasticsearch/logs:/usr/share/elasticsearch/logs \
-e ES_JAVA_OPTS="-Xms1024m -Xmx1024m" \
-e TAKE_FILE_OWNERSHIP=true \
docker.elastic.co/elasticsearch/elasticsearch:7.15.2
```

### 生成密钥

```
# 进入容器
docker exec -it prod_loganalysis_es_01 /bin/bash
# 生成密钥
bin/elasticsearch-certutil cert -out config/elastic-certificates.p12 -pass ""
cd /usr/share/elasticsearch/config/
chmod 777 elastic-certificates.p12
exit

# 复制到宿主机
docker cp prod_loganalysis_es_01:/usr/share/elasticsearch/config/elastic-certificates.p12 /docker_data/loganalysis_elasticsearch/config/elastic-certificates.p12 
```

### 分发密钥
2-6主机均需复制
```
docker cp /docker_data/loganalysis_elasticsearch/config/elastic-certificates.p12 prod_loganalysis_es_03:/usr/share/elasticsearch/config/elastic-certificates.p12

# 修改/docker_data/elasticsearch/config/elasticsearch.yml，添加以下几行：
xpack.security.enabled: true
xpack.license.self_generated.type: basic
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
```
重启docker-es实例
docker restart prod_loganalysis_es_01

### 修改密码

```
[root@node1 ~]# docker exec -it prod_loganalysis_es_01 /bin/bash
bash-4.4# bin/elasticsearch-setup-passwords interactive
Initiating the setup of passwords for reserved users elastic,apm_system,kibana,kibana_system,logstash_system,beats_system,remote_monitoring_user.
You will be prompted to enter passwords as the process progresses.
Please confirm that you would like to continue [y/N]y

```

### 验证集群
打开：http://192.168.10.188:9200/_cat/nodes?pretty，验证新设密码（elastic/szt@#es）。
```
192.168.10.187 52 86 0 0.16 0.20 0.18 cdfhilmrstw * loganalysis-es-187
192.168.10.186 42 99 0 0.25 0.35 0.28 cdfhilmrstw - loganalysis-es-186
192.168.10.188 29 70 0 0.08 0.21 0.22 cdfhilmrstw - loganalysis-es-188
```
