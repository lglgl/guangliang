
https://www.elastic.co/guide/en/enterprise-search/7.15/docker.html

### 前置工作
创建外置目录，修改进程可使用最大虚拟内存
```
mkdir -p /docker_data/elasticsearch/{config,data,plugins,logs}
echo "vm.max_map_count=655300" >> /etc/sysctl.conf
cat /etc/sysctl.conf
sysctl -p

#vm.max_map_count = 655300
```
### 创建配置文件

```shell
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

# 指定该节点是否存储索引数据
node.master: true
node.data: true

# 设置默认索引分片个数 6*3
index.number_of_shards: 18
# 设置默认索引副本个数
index.number_of_replicas: 1
# 设置为true来锁住内存不进行swapping
bootstrap.mlockall: true
# 支持跨域，不设置也不影响
http.cors.enabled: true
http.cors.allow-origin: "*"

# 集群证书相关
xpack.security.enabled: true
xpack.license.self_generated.type: basic
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
```




PUT /_template/log
{
  "template": "*",
  "settings": {
    "number_of_shards": 5,
    "number_of_replicas": "1"
  }
}