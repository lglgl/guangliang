

### docker-compose
```shell
  prod_cluster_zookeeper_01:
    hostname: idszt-node001
    image: ${zookeeper_image}
    network_mode: host
    volumes:
      - /docker_data/zookeeper/conf:/conf
      - /docker_data/zookeeper/data:/data
      - /docker_data/zookeeper/datalog:/datalog
      - /etc/localtime:/etc/localtime

```

### 统一集群配置文件
每台都一样
zoo.cfg
```shell
# CS通信心跳
tickTime=2000
# 初始通信时限
initLimit=10
# 服务器之间请求和应答
syncLimit=5

dataDir=/data
dataLogDir=/datalog
# 端口，默认2181
clientPort=2181
# 最大客户端链接数量，0不限制，默认是0
maxClientCnxns=100
# 关闭启动内置的管理器8080端口
admin.enableServer=false
# 开启自动清理，保留10天，500个文件
autopurge.snapRetainCount=500
autopurge.purgeInterval=240

server.1=192.168.10.181:2888:3888
server.2=192.168.10.182:2888:3888
server.3=192.168.10.183:2888:3888
server.4=192.168.10.184:2888:3888
server.5=192.168.10.185:2888:3888
server.6=192.168.10.186:2888:3888

# 打开prometheus监控
metricsProvider.className=org.apache.zookeeper.metrics.prometheus.PrometheusMetricsProvider
metricsProvider.httpPort=7000
metricsProvider.exportJvmInfo=true
```

防火墙
https://www.cnblogs.com/zlonger/p/16195799.html


### 启动过程

2023-07-14 14:25:50,778 - INFO  [main:ZKAuditProvider@42] - ZooKeeper audit is disabled.
2023-07-14 14:25:50,785 - INFO  [QuorumPeer[myid=3](plain=0.0.0.0:12181)(secure=disabled):QuorumPeer@1438] - LOOKING
2023-07-14 14:25:50,787 - INFO  [QuorumPeer[myid=3](plain=0.0.0.0:12181)(secure=disabled):FastLeaderElection@945] - New election. My id = 3, proposed zxid=0x0
2023-07-14 14:25:50,798 - INFO  [WorkerReceiver[myid=3]:FastLeaderElection$Messenger$WorkerReceiver@390] - Notification: my state:LOOKING; n.sid:3, n.state:LOOKING, n.leader:3, n.round:0x1, n.peerEpoch:0x0, n.zxid:0x0, message format version:0x2, n.config version:0x0
2023-07-14 14:25:50,808 - INFO  [WorkerReceiver[myid=3]:FastLeaderElection$Messenger$WorkerReceiver@390] - Notification: my state:LOOKING; n.sid:1, n.state:LOOKING, n.leader:1, n.round:0x3, n.peerEpoch:0x0, n.zxid:0x0, message format version:0x2, n.config version:0x0
2023-07-14 14:25:50,809 - INFO  [WorkerReceiver[myid=3]:FastLeaderElection$Messenger$WorkerReceiver@390] - Notification: my state:LOOKING; n.sid:2, n.state:LOOKING, n.leader:2, n.round:0x2, n.peerEpoch:0x0, n.zxid:0x0, message format version:0x2, n.config version:0x0
2023-07-14 14:25:50,812 - INFO  [WorkerReceiver[myid=3]:FastLeaderElection$Messenger$WorkerReceiver@390] - Notification: my state:LOOKING; n.sid:2, n.state:LEADING, n.leader:2, n.round:0x2, n.peerEpoch:0x0, n.zxid:0x0, message format version:0x2, n.config version:0x0
2023-07-14 14:25:50,813 - INFO  [WorkerReceiver[myid=3]:FastLeaderElection$Messenger$WorkerReceiver@390] - Notification: my state:LOOKING; n.sid:1, n.state:LOOKING, n.leader:1, n.round:0x3, n.peerEpoch:0x0, n.zxid:0x0, message format version:0x2, n.config version:0x0
2023-07-14 14:25:50,814 - INFO  [WorkerReceiver[myid=3]:FastLeaderElection$Messenger$WorkerReceiver@390] - Notification: my state:LOOKING; n.sid:3, n.state:LOOKING, n.leader:3, n.round:0x3, n.peerEpoch:0x0, n.zxid:0x0, message format version:0x2, n.config version:0x0
2023-07-14 14:25:50,816 - INFO  [WorkerReceiver[myid=3]:FastLeaderElection$Messenger$WorkerReceiver@390] - Notification: my state:LOOKING; n.sid:2, n.state:LEADING, n.leader:2, n.round:0x2, n.peerEpoch:0x0, n.zxid:0x0, message format version:0x2, n.config version:0x0
2023-07-14 14:25:50,817 - INFO  [WorkerReceiver[myid=3]:FastLeaderElection$Messenger$WorkerReceiver@390] - Notification: my state:LOOKING; n.sid:1, n.state:LOOKING, n.leader:3, n.round:0x3, n.peerEpoch:0x0, n.zxid:0x0, message format version:0x2, n.config version:0x0
2023-07-14 14:25:50,818 - INFO  [WorkerReceiver[myid=3]:FastLeaderElection$Messenger$WorkerReceiver@390] - Notification: my state:LOOKING; n.sid:2, n.state:LEADING, n.leader:2, n.round:0x2, n.peerEpoch:0x0, n.zxid:0x0, message format version:0x2, n.config version:0x0
2023-07-14 14:25:51,019 - INFO  [QuorumPeer[myid=3](plain=0.0.0.0:12181)(secure=disabled):QuorumPeer@902] - Peer state changed: leading
2023-07-14 14:25:51,019 - INFO  [QuorumPeer[myid=3](plain=0.0.0.0:12181)(secure=disabled):QuorumPeer@1532] - LEADING