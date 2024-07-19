

## 2、端口说明

- 探针：
- Nacos:8848/9848/9849



## 3、防火墙设置

```shell
# 查看规则
firewall-cmd --list-all
firewall-cmd --reload
systemctl restart firewalld.service
systemctl stop firewalld.service
systemctl start firewalld.service
systemctl status firewalld.service
# 删除规则
firewall-cmd --permanent --remove-rich-rule="rule family="ipv4" source address="192.168.10.66" port protocol="tcp" port="80" accept"
firewall-cmd --permanent --remove-rich-rule='rule family="ipv4" source address="192.168.240.0/24" port port="22" protocol="tcp" accept'


# 添加规则（主机）
firewall-cmd --permanent --add-source=192.168.140.12
firewall-cmd --reload
firewall-cmd --permanent --add-source=192.168.240.222
firewall-cmd --reload
firewall-cmd --permanent --remove-port=21191/tcp
firewall-cmd --reload
firewall-cmd --permanent --remove-source=192.168.240.218

# Elasticsearch
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.10.66" port protocol="tcp" port="80" accept"
firewall-cmd --reload

firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="59.36.150.13" port protocol="tcp" port="18848" accept'
firewall-cmd --reload

firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.223" port protocol="tcp" port="16379" accept'
firewall-cmd --reload

firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.222" port protocol="tcp" port="8195" accept'
firewall-cmd --reload


firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.225" port protocol="tcp" port="3309" accept'
firewall-cmd --reload

# Kafka
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.222" port protocol="tcp" port="10021" accept'
firewall-cmd --reload
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.218" port port="50000-55000" protocol="tcp" accept'
firewall-cmd --reload

firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.223" port port="2011-2012" protocol="tcp" accept'
firewall-cmd --reload

firewall-cmd --permanent --add-port=21191/tcp
firewall-cmd --reload



firewall-cmd --remove-rich-rule  '' --permanent
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.218" port port="3309" protocol="tcp" accept'
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.223" port port="3000" protocol="tcp" accept'
firewall-cmd --reload

## 添加ip有效
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.216"   accept" 
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.212" accept"
firewall-cmd --reload
# Minio
cat /etc/firewalld/zones/public.xml


# Nacos
# Redis


# Zookeeper
## node001
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.212" port protocol="tcp" port="12181" accept'





# MySQL
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.240.220" port protocol="tcp" port="18848" accept'
firewall-cmd --reload && firewall-cmd --list-all


```
firewall-cmd --list-all && firewall-cmd --reload


netstat -lnpt 

netstat -nat | grep -i :22

tcpkill -i any -9 host 49.7.40.205

sudo tcpkill -i any 'port 22'

netstat -antp|grep tcp[^6]


route add -host 192.168.140.11 gw 192.168.240.1
route del -host 192.168.140.0 gw 192.168.240.1

#添加一条路由(发往192.168.140.0这个网段的全部要经过网关192.168.240.1)
route add -net 192.168.140.0 netmask 255.255.255.0 gw 192.168.240.1
1、第一点：机房暂未提供堡垒机（外部原因）
2、第三点：nacos不打算升级,因当前版本是官方建议的稳定版本。该组件不对集群主机外提供服务，已发现的漏洞存在于对安全不产生影响。
3、第四点：大部分已修改（暂无法完全使用加密字符串）


ansible-playbook /etc/ansible/script/commom/op


firewall-cmd --list-all|grep '240.22[0-9]'


firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.212" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.220" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.222" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.223" accept"
firewall-cmd --reload

firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.211" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.220" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.222" accept"
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.240.223" accept"
firewall-cmd --reload