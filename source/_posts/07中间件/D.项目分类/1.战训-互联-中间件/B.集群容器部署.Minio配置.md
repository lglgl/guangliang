参数文档：https://min.io/docs/minio/linux/reference/minio-server/minio-server.html#command-minio


压测工具：https://github.com/minio/warp

```shell
[root@idszt-node008 ~]# docker pull minio/warp
Using default tag: latest
latest: Pulling from minio/warp
31e352740f53: Pull complete
989ffa3c5cfb: Pull complete
Digest: sha256:2b750dac72d3c7b14228d8476154e3b7a45ef53bc8f044a6e81320e56cbcf616
Status: Downloaded newer image for minio/warp:latest
docker.io/minio/warp:latest
[root@idszt-node008 ~]# docker image inspect minio/warp:latest |grep -i version
        "DockerVersion": "20.10.21",
```

```shell
docker run --net host --name warp\
  -v /home/test/result:/result \
  minio/warp put --host=192.168.10.18{1...6}:9000 --access-key=minioadmin --secret-key=minioadmin \
  --benchdata /result/put-100 \
  --concurrent 20 \
  --obj.randsize \
  --duration 10m0s \
  --autoterm 


[root@idszt-node008 ~]# docker run --net host --name warp\
>   -v /home/test/result:/result \
>   minio/warp put --host=192.168.10.18{1...6}:9000 --access-key=minioadmin --secret-key=minioadmin \
>   --benchdata /result/put-100 \
>   --concurrent 20 \
>   --obj.randsize \
>   --duration 10m0s \
>   --autoterm
warp: Preparing server.
Clearing Prefix "warp-benchmark-bucket/"...warp: Starting benchmark in 3s...
warp: Benchmark starting...
Throughput 92.6MiB/s within 7.500000% for 22.442s. Assuming stability. Terminating benchmark.
warp: Saving benchmark data...
warp: Benchmark data written to "/result/put-100.csv.zst"


----------------------------------------
Operation: PUT. Concurrency: 20
* Average: 82.16 MiB/s, 47.68 obj/s

Throughput by host:
 * http://192.168.10.181:9000: Avg: 14.11 MiB/s, 8.48 obj/s
 * http://192.168.10.182:9000: Avg: 14.11 MiB/s, 7.96 obj/s
 * http://192.168.10.183:9000: Avg: 13.32 MiB/s, 7.64 obj/s
 * http://192.168.10.184:9000: Avg: 13.79 MiB/s, 7.87 obj/s
 * http://192.168.10.185:9000: Avg: 12.59 MiB/s, 7.93 obj/s
 * http://192.168.10.186:9000: Avg: 14.37 MiB/s, 7.90 obj/s

Throughput, split into 81 x 1s:
 * Fastest: 119.4MiB/s, 82.85 obj/s
 * 50% Median: 89.3MiB/s, 46.15 obj/s
 * Slowest: 21.8MiB/s, 9.92 obj/s
warp: Starting cleanup...
Clearing Prefix "warp-benchmark-bucket/NLlrt35y/"...warp: Cleanup Done.
```


### 生产环境压测
docker run --net host --name warp\
  -v /home/test/result:/result \
  minio/warp put --host=192.168.240.21{1...4}:9000 --access-key=minioadmin --secret-key=minio#SZT6760 \
  --benchdata /result/put-100 \
  --concurrent 20 \
  --obj.randsize \
  --duration 2m0s \
  --autoterm 