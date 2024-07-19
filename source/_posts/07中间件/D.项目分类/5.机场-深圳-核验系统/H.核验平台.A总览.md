### 版本







docker exec -it redismaster_prod_verifyplatform_redis_01_1 /bin/bash
docker-compose exec redismaster_prod_verifyplatform_redis_01_1 bash

redis-cli -h 192.168.10.191 -p 16379 -a redis#SZT6542 cluster nodes

# cluster-replicas 1 表示每个主节点需要1个从节点




redis-cli --cluster create 192.168.10.191:16379 192.168.10.192:16379 192.168.10.193:16379 --cluster-replicas 0 -a redis#SZT6542


redis-cli --cluster add-node 192.168.10.191:16380 192.168.10.191:16379  --cluster-slave --cluster-master-id f36a947d8e71f6cea792edbe506f2731a7e07dd6 -a redis#SZT6542

redis-cli --cluster add-node 192.168.10.192:16380 192.168.10.192:16379  --cluster-slave --cluster-master-id 52088be9d63006722e8f5a71cf207a7d9ebf6bcc -a redis#SZT6542

redis-cli --cluster add-node 192.168.10.193:16380 192.168.10.193:16379  --cluster-slave --cluster-master-id f25071cfa4a22e3f1b0032c0da51cbc1ad5c26c4 -a redis#SZT6542


root@docker-node201:/data# redis-cli --cluster add-node 192.168.10.193:16380 192.168.10.193:16379  --cluster-slave --cluster-master-id f25071cfa4a22e3f1b0032c0da51cbc1ad5c26c4 -a redis#SZT6542
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
>>> Adding node 192.168.10.193:16380 to cluster 192.168.10.193:16379
>>> Performing Cluster Check (using node 192.168.10.193:16379)
M: f25071cfa4a22e3f1b0032c0da51cbc1ad5c26c4 192.168.10.193:16379
   slots:[10923-16383] (5461 slots) master
S: 15ee856c2dab5c3270c0fe69f713faf754ce0cd0 192.168.10.191:16380
   slots: (0 slots) slave
   replicates f36a947d8e71f6cea792edbe506f2731a7e07dd6
M: f36a947d8e71f6cea792edbe506f2731a7e07dd6 192.168.10.191:16379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 52088be9d63006722e8f5a71cf207a7d9ebf6bcc 192.168.10.192:16379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: fa51266545cca84e38e9aa943a4be35a21a8a063 192.168.10.192:16380
   slots: (0 slots) slave
   replicates 52088be9d63006722e8f5a71cf207a7d9ebf6bcc
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
>>> Send CLUSTER MEET to node 192.168.10.193:16380 to make it join the cluster.
Waiting for the cluster to join

>>> Configure node as replica of 192.168.10.193:16379.
[OK] New node added correctly.
