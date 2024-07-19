
## MHA高可用

```
ssh-keygen -t rsa
cd ~/.ssh
ssh-copy-id idszt-node101 -p 21191
ssh-copy-id idszt-node102 -p 21191

[root@idszt-node101 keepalived]# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:SpKozMuJ2OoSMpiy7TTIgZt3VS6VpTi2YfVR2U/bwuk root@idszt-node101
The key's randomart image is:
+---[RSA 2048]----+
|        . o..o   |
|       o = .. . .|
|      * = .  . +o|
|.  . + B      +.o|
|oo. o = S    . . |
|@=.  + o      E  |
|BO+ . .          |
|*++o             |
|*Bo              |
+----[SHA256]-----+
[root@idszt-node101 keepalived]# cd ~/.ssh
[root@idszt-node101 .ssh]# ssh-copy-id idszt-node102 -p 21191
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
The authenticity of host '[idszt-node102]:21191 ([192.168.140.12]:21191)' can't be established.
ECDSA key fingerprint is SHA256:EjQXTGkDoR7wp0N7TVinY0l4wDJfTbD8+ykzu9bEMhA.
ECDSA key fingerprint is MD5:46:04:08:bd:2c:98:2e:b9:ff:e6:a6:40:37:94:15:46.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@idszt-node102's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh -p '21191' 'idszt-node102'"
and check to make sure that only the key(s) you wanted were added.

[root@idszt-node101 .ssh]# ssh -p '21191' 'idszt-node102'
Last login: Mon Aug  7 11:33:12 2023 from 192.168.240.222
[root@idszt-node102 ~]# exit
登出
Connection to idszt-node102 closed.

```



keepalive
/etc/keepalived/keepalived.conf
```
! Configuration File for keepalived

global_defs {
   router_id idszt-node101
}

vrrp_instance VI_1 {
    state MASTER
    interface enp2s0f1
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.140.14
    }
}

virtual_server 192.168.10.14 3306 {
    delay_loop 6
    lb_algo rr
    lb_kind DR
    net_mask 255.255.255.0
    persistence_timeout 50
    protocol TCP

    real_server 192.168.10.11 3309 {
        weight 1
        notify_down /etc/keepalived/closekeepalived.sh
        TCP_CHECK {               # 健康检查
            connect_timeout 3
            nb_get_retry 3
            delay_before_retry 3
            connect_port 3309     # 设置连接端口3309
        }
    }
}

```



关闭keepalived

```
/etc/keepalived/closekeepalived.sh

#closekeepalived.sh内容
sudo -S killall keepalived

chmod +x /etc/keepalived/closekeepalived.sh
```



测试

原理：keepalived监控自己节点的mysql是否正常，如果检测mysql已关闭，则关闭自己告诉对方接管虚拟IP。

```
[root@node71 mha]# service keepalived restart
systemctl status keepalived.service
Redirecting to /bin/systemctl restart keepalived.service
[root@node71 mha]# ps -ef | grep keepalived
root      27246      1  0 00:04 ?        00:00:00 /usr/sbin/keepalived -D
root      27247  27246  0 00:04 ?        00:00:00 /usr/sbin/keepalived -D
root      27248  27246  0 00:04 ?        00:00:00 /usr/sbin/keepalived -D
root      27255  23305  0 00:04 pts/0    00:00:00 grep --color=auto keepalived
[root@node71 mha]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens32: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:fd:2c:2f brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.71/24 brd 192.168.10.255 scope global noprefixroute ens32
       valid_lft forever preferred_lft forever
    inet 192.168.10.77/32 scope global ens32
       valid_lft forever preferred_lft forever
    inet6 fe80::9c86:aaa1:7ab7:4315/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:6b:1e:21 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN group default qlen 1000
    link/ether 52:54:00:6b:1e:21 brd ff:ff:ff:ff:ff:ff


```





## 维护操作

```
[root@node71 ~]# systemctl restart mysqld
[root@node71 ~]# systemctl status mysqld
[root@node71 ~]# systemctl stop mysqld
[root@node71 ~]# systemctl restart keepalived.service
[root@node71 ~]# systemctl stop keepalived.service
systemctl status keepalived.service
```



#!/bin/bash
systemctl stop keepalived.service
#systemctl start keepalived.service
echo "已关闭keepalived！"
ps | grep keepalived
systemctl status keepalived.service
echo "keepalived已关闭，请检查！" >> /etc/keepalived/keepalived.log
echo $(date "+%Y-%m-%d %H:%M:%S") >> /etc/keepalived/keepalived.log
echo "已关闭，请检查！"

------SELinux配置-----------

journalctl -xe

SELinux is preventing closekeepalived from getattr access on the file /usr/bin/systemctl. For complete SELinux messages run: sealert -l 59171f78-9a27-48da-aab


ausearch -c 'ksmtuned' --raw | audit2allow -M my-ksmtuned && semodule -i my-ksmtuned.pp
ausearch -c 'mysql' --raw | audit2allow -M my-mysql && semodule -i my-mysql.pp

ausearch -c 'closekeepalived' --raw | audit2allow -M my-closekeepalived && semodule -i my-closekeepalived.pp

setsebool -P daemons_enable_cluster_mode 1


```shell
[root@idszt-node101 keepalived]# systemctl status keepalived.service
● keepalived.service - LVS and VRRP High Availability Monitor
   Loaded: loaded (/usr/lib/systemd/system/keepalived.service; disabled; vendor preset: disabled)
   Active: active (running) since 一 2023-08-07 16:08:50 CST; 1min 39s ago
  Process: 25049 ExecStart=/usr/sbin/keepalived $KEEPALIVED_OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 25051 (keepalived)
    Tasks: 3
   Memory: 1.7M
   CGroup: /system.slice/keepalived.service
           ├─25051 /usr/sbin/keepalived -D
           ├─25052 /usr/sbin/keepalived -D
           └─25053 /usr/sbin/keepalived -D

8月 07 16:08:52 idszt-node101 Keepalived_vrrp[25053]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 16:08:52 idszt-node101 Keepalived_vrrp[25053]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 16:08:52 idszt-node101 Keepalived_vrrp[25053]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 16:08:52 idszt-node101 Keepalived_vrrp[25053]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 16:08:57 idszt-node101 Keepalived_vrrp[25053]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 16:08:57 idszt-node101 Keepalived_vrrp[25053]: VRRP_Instance(VI_1) Sending/queueing gratuitous ARPs on enp2s0f1 for 192.168.140.14
8月 07 16:08:57 idszt-node101 Keepalived_vrrp[25053]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 16:08:57 idszt-node101 Keepalived_vrrp[25053]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 16:08:57 idszt-node101 Keepalived_vrrp[25053]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 16:08:57 idszt-node101 Keepalived_vrrp[25053]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14


[root@idszt-node102 keepalived]# systemctl status keepalived.service
● keepalived.service - LVS and VRRP High Availability Monitor
   Loaded: loaded (/usr/lib/systemd/system/keepalived.service; disabled; vendor preset: disabled)
   Active: active (running) since 一 2023-08-07 15:53:38 CST; 16min ago
  Process: 32409 ExecStart=/usr/sbin/keepalived $KEEPALIVED_OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 32410 (keepalived)
    Tasks: 3
   Memory: 1.9M
   CGroup: /system.slice/keepalived.service
           ├─32410 /usr/sbin/keepalived -D
           ├─32411 /usr/sbin/keepalived -D
           └─32412 /usr/sbin/keepalived -D

8月 07 15:57:51 idszt-node102 Keepalived_vrrp[32412]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 15:57:51 idszt-node102 Keepalived_vrrp[32412]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 15:57:51 idszt-node102 Keepalived_vrrp[32412]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 15:57:51 idszt-node102 Keepalived_vrrp[32412]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 15:57:56 idszt-node102 Keepalived_vrrp[32412]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 15:57:56 idszt-node102 Keepalived_vrrp[32412]: VRRP_Instance(VI_1) Sending/queueing gratuitous ARPs on enp2s0f1 for 192.168.140.14
8月 07 15:57:56 idszt-node102 Keepalived_vrrp[32412]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 15:57:56 idszt-node102 Keepalived_vrrp[32412]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 15:57:56 idszt-node102 Keepalived_vrrp[32412]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14
8月 07 15:57:56 idszt-node102 Keepalived_vrrp[32412]: Sending gratuitous ARP on enp2s0f1 for 192.168.140.14


```

---防火墙组播----
firewall-cmd --direct --permanent --add-rule ipv4 filter INPUT 0 --in-interface  enp2s0f1 --destination 224.0.0.18 --protocol vrrp -j ACCEPT;
firewall-cmd --reload















```shell

! Configuration File for keepalived

global_defs {
   router_id node101
}

vrrp_script check_run {
   script "/etc/keepalived/closekeepalived.sh"
   interval 2
}

vrrp_instance VI_1 {
    state MASTER
    interface enp2s0f1
    virtual_router_id 51
    priority 201
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
        }
    virtual_ipaddress {
        192.168.140.14
        }
    track_script {
        check_run
        }
    }



! Configuration File for keepalived

global_defs {
   router_id node102
}

vrrp_script check_run {
   script "/etc/keepalived/closekeepalived.sh"
   interval 2
}

vrrp_instance VI_1 {
    state BACKUP
    interface enp2s0f1
    virtual_router_id 51
    priority 102
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
        }
    virtual_ipaddress {
        192.168.140.14
        }
    track_script {
        check_run
        }
}



```