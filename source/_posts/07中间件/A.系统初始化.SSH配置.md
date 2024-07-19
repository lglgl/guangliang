SSH远程接入配置
### 1.前置条件

CentOS7.9系统安装结束。

### 2.调整SSH默认端口

配置文件调整/etc/ssh/sshd_config

```
vi /etc/ssh/sshd_config

#port 22
#改为
port 21191
```

### 3.调整防火墙策略

- 可手动图形化界面加入

- 可用以下命令行加入

  ```
  #向防火墙中添加端口的命令为
  firewall-cmd --zone=public --add-port=21191/tcp --permanent
  
  #reaload 防火墙规则
  firewall-cmd --reload
  
  #查看端口是否添加成功
  firewall-cmd --zone=public --query-port=21191/tcp
  ```

### 4.调整SELINUX内部端口策略



### 5.重启SSH程序

- 确认状态正常`active`，和端口`Server listening on 0.0.0.0 port 21191`

```
#重启服务
systemctl restart sshd

#确认状态正常active，和端口`Server listening on 0.0.0.0 port 21191`
[root@idszt-node008 ~]# systemctl status sshd
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since 日 2023-06-04 16:16:25 CST; 8min ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 2107 (sshd)
    Tasks: 1
   CGroup: /system.slice/sshd.service
           └─2107 /usr/sbin/sshd -D

6月 04 16:16:25 idszt-node008 systemd[1]: Starting OpenSSH server daemon...
6月 04 16:16:25 idszt-node008 sshd[2107]: Server listening on 0.0.0.0 port 21191.
6月 04 16:16:25 idszt-node008 sshd[2107]: Server listening on :: port 21191.
6月 04 16:16:25 idszt-node008 systemd[1]: Started OpenSSH server daemon.
6月 04 16:17:07 idszt-node008 sshd[3366]: Accepted password for root from 223.104.77.180 port 59879 ssh2
6月 04 16:18:06 idszt-node008 sshd[3393]: Accepted password for root from 223.104.77.180 port 53327 ssh2
6月 04 16:18:12 idszt-node008 sshd[3401]: Accepted password for root from 223.104.77.180 port 56479 ssh2
```

### 6.验证SSH接入

远程客户端通过移动互联网，SSH访问公网IP接入，显示正常。



修改端口

/etc/ssh/sshd_config

放开21191端口



systemctl restart sshd



查询当前 ssh 服务端口: `semanage port -l | grep ssh`

向 SELinux 中添加 ssh 端口: `semanage port -a -t ssh_port_t -p tcp 21191`





3.向防火墙中添加修改的端口
向防火墙中添加端口的命令为: firewall-cmd --zone=public --add-port=21191/tcp --permanent

reaload 防火墙规则: firewall-cmd --reload

查看端口是否添加成功: firewall-cmd --zone=public --query-port=21191/tcp
