

### 组网目的
将docker不同宿主机汇聚在一个网络内连接，降低维护成本。

### 默认网络

```shell
[root@idszt-node005 ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
54cee69bd6b6   bridge    bridge    local
9a826cba5990   host      host      local
aabff5f20fd9   none      null      local


[root@idszt-node005 ~]# docker inspect 54cee69bd6b6
[
    {
        "Name": "bridge",
        "Id": "54cee69bd6b6a5484537467aaeb9378c47bd01b73752aabb4ea24ffe876e78e6",
        "Created": "2023-06-20T09:35:45.860337108+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "8fd4277e288fd5bdc1caa70dfc6aff7027c1ddfc10c9e65c9c04e5062427c150": {
                "Name": "prod_nacos_cluster_05",
                "EndpointID": "42ca672d2068ffac1e8b3e712e1005740e916eec18eb7b1cb915514bc5ff304a",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]

```


### 组网方法
修改docker0网段，宿主机添加静态路由。

```shell
#/etc/docker/daemon.json添加后，重启docker
"bip":"172.92.0.1/16",

systemctl restart docker.service



route add -net 172.91.0.0 netmask 255.255.0.0 gw 192.168.10.91
route add -net 172.92.0.0 netmask 255.255.0.0 gw 192.168.10.92

--------k8s----------


192.168.10.91 idszt-node91
192.168.10.92 idszt-node92
192.168.10.93 idszt-node93


kubeadm init   --apiserver-advertise-address=192.168.10.91 --image-repository registry.aliyuncs.com/google_containers   --kubernetes-version v1.16.0   --service-cidr=172.191.0.0/16 --pod-network-cidr=172.181.0.0/16


kubeadm join 192.168.10.91:6443 --token fy0m1i.p5hq6in42ulnbumy \
    --discovery-token-ca-cert-hash sha256:3fbe8189bec25cd317378c7e2a22de94218141db9ee51b95c7582d4b67e0b57a