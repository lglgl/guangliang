
https://www.elastic.co/guide/en/enterprise-search/7.15/docker.html

### 前置工作
创建外置目录，修改进程可使用最大虚拟内存
```
mkdir -p /docker_data/elasticsearch/{config,data,plugins,logs}
echo "vm.max_map_count=262144" >> /etc/sysctl.conf
cat /etc/sysctl.conf
sysctl -p

#vm.max_map_count = 262144
```
### 创建配置文件
每个节点`/docker_data/elasticsearch/config/elasticsearch.yml`,修改node.name，network.publish_host
```
#配置es的集群名称，默认是elasticsearch，es会自动发现在同一网段下的es，如果在同一网段下有多个集群，就可以用这个属性来区分不同的集群。
cluster.name: prod-env-es
#节点名称
node.name: es-182
#设置索引数据的存储路径
path.data: ./data
#设置日志的存储路径
path.logs: ./logs
#设置当前的ip地址,通过指定相同网段的其他节点会加入该集群中
network.host: 0.0.0.0
network.publish_host: 192.168.10.182
#设置对外服务的http端口
http.port: 9200
transport.tcp.port: 9300
#设置集群中master节点的初始列表，可以通过这些节点来自动发现新加入集群的节点
#discovery.zen.ping.unicast.hosts: ["0.0.0.0"]
discovery.seed_hosts: ["192.168.10.181:9300","192.168.10.182:9300","192.168.10.183:9300","192.168.10.184:9300","192.168.10.185:9300","192.168.10.186:9300"]
cluster.initial_master_nodes: ["es-181", "es-182","es-183", "es-184", "es-185", "es-186"]

node.master: true
node.data: true

http.cors.enabled: true
http.cors.allow-origin: "*"
## java.lang.IllegalArgumentException: node settings must not contain any index level settings
## 设置默认索引副本个数
#index.number_of_replicas: 1
## 设置默认索引分片个数 5
#index.number_of_shards: 5
## 索引缓存
#indices.memory.index_buffer_size: 512m

```


### 创建实例
```shell
# 记得修改名称和ES_JAVA_OPTS
docker run  --name prod_es_cluster_01 \
--network=host --privileged=true --restart=always \
-v /docker_data/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /docker_data/elasticsearch/data:/usr/share/elasticsearch/data \
-v /docker_data/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-v /docker_data/elasticsearch/logs:/usr/share/elasticsearch/logs \
-e ES_JAVA_OPTS="-Xms1024m -Xmx1024m" \
-e TAKE_FILE_OWNERSHIP=true \
docker.elastic.co/elasticsearch/elasticsearch:7.15.2
```

### 生成密钥

```
# 进入容器
docker exec -it prod_es_cluster_01 /bin/bash


# 生成密钥
bin/elasticsearch-certutil cert -out config/elastic-certificates.p12 -pass ""
cd /usr/share/elasticsearch/config/
chmod 777 elastic-certificates.p12
exit

# 复制到宿主机
docker cp prod_es_cluster_01:/usr/share/elasticsearch/config/elastic-certificates.p12 /docker_data/elasticsearch/config/elastic-certificates.p12 
```

### 分发密钥
2-6主机均需复制
```
docker cp /docker_data/elasticsearch/config/elastic-certificates.p12 prod_es_cluster_02:/usr/share/elasticsearch/config/elastic-certificates.p12

# 修改/docker_data/elasticsearch/config/elasticsearch.yml，添加以下几行：
xpack.security.enabled: true
xpack.license.self_generated.type: basic
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
```
重启docker-es实例


### 修改密码

```shell
docker-compose exec prod_middleware_es_02 bash
docker-compose exec prod_middleware_es_02 bash
[root@node1 ~]# docker-compose exec prod_middleware_es_02 bash
bash-4.4# bin/elasticsearch-setup-passwords interactive
Initiating the setup of passwords for reserved users elastic,apm_system,kibana,kibana_system,logstash_system,beats_system,remote_monitoring_user.
You will be prompted to enter passwords as the process progresses.
Please confirm that you would like to continue [y/N]y
elastic#SZT5764
```

### 验证集群
打开：http://192.168.240.214:19200/_cat/nodes?pretty，输入新设密码。

```
192.168.10.186 58 99 0 0.00 0.07 0.21 cdfhilmrstw - es-186
192.168.10.185 66 68 0 0.00 0.10 0.28 cdfhilmrstw * es-185
192.168.10.183 56 88 0 0.03 0.05 0.06 cdfhilmrstw - es-183
192.168.10.184 42 98 0 0.07 0.07 0.08 cdfhilmrstw - es-184
192.168.10.181 15 63 0 0.27 0.09 0.16 cdfhilmrstw - es-181
192.168.10.182 54 57 9 1.39 0.63 0.56 cdfhilmrstw - es-182
```

### 可选安装kibana

```
docker run -d --name dev-kibana-standalone-01  -p 5601:5601 -e "ELASTICSEARCH_HOSTS=http://119.23.155.163:9200/" docker.elastic.co/kibana/kibana:7.15.2

docker exec -it dev-kibana-standalone-01 /bin/bash		# 进入容器内部
cat /usr/share/kibana/config/kibana.yml


# Default Kibana configuration for docker target
server.host: "0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
monitoring.ui.container.elasticsearch.enabled: true

i18n.locale: zh-CN
#elasticsearch.username: elastic
#elasticsearch.password: szt@#es

```

### 错误处理
原因：新实例共享使用了旧实例的数据文件夹
解决方法：停止docker es实例，删除data和logs文件夹内数据，重新run.
```
Exception in thread "main" java.lang.RuntimeException: starting java failed with [1]
output:
[0.000s][error][logging] Error opening log file 'logs/gc.log': Permission denied
[0.000s][error][logging] Initialization of output 'file=logs/gc.log' using options 'filecount=32,filesize=64m' failed.
error:
Invalid -Xlog option '-Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,pid,tags:filecount=32,filesize=64m', see error log for details.
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
        at org.elasticsearch.tools.launchers.JvmOption.flagsFinal(JvmOption.java:119)
        at org.elasticsearch.tools.launchers.JvmOption.findFinalOptions(JvmOption.java:81)
        at org.elasticsearch.tools.launchers.JvmErgonomics.choose(JvmErgonomics.java:38)
        at org.elasticsearch.tools.launchers.JvmOptionsParser.jvmOptions(JvmOptionsParser.java:135)
        at org.elasticsearch.tools.launchers.JvmOptionsParser.main(JvmOptionsParser.java:86)
```

原因：/usr/share/elasticsearch/config/elastic-certificates.p12创建后没有访问权限。
解决方法：进入容器，chmod 777暴力授权。
```
"stacktrace": ["org.elasticsearch.bootstrap.StartupException: ElasticsearchSecurityException[failed to load SSL configuration [xpack.security.transport.ssl]]; nested: ElasticsearchException[failed to initialize SSL TrustManager - not permitted to read truststore file [/usr/share/elasticsearch/config/elastic-certificates.p12]]; 
```


prod_middleware_es_01_1  | {"type": "server", "timestamp": "2023-07-21T06:46:19,857Z", "level": "ERROR", "component": "o.e.b.ElasticsearchUncaughtExceptionHandler", "cluster.name": "prod-middleware-elasticsearch", "node.name": "middleware-es-node001", "message": "uncaught exception in thread [main]",


解决方法：原来的docker没停掉。停止然后删除就行。



node settings must not contain any index level settings

副本数
分片数
