
yum localinstall *.rpm -y


firewall-cmd --direct --permanent --add-rule ipv4 filter INPUT 0 --in-interface  enp2s0f1 --destination 224.0.0.18 --protocol vrrp -j ACCEPT;
firewall-cmd --reload


```shell
# /etc/keepalived/closekeepalived.sh

#!/bin/bash
/usr/bin/mysql -uroot -pmysql#SZT7468 -e "show status" &>/dev/null
if [ $? -ne 0 ] ;then
    systemctl stop keepalived && echo "keepalived已关闭，请检查！"+ $(date "+%Y-%m-%d %H:%M:%S") >> /etc/keepalived/keepalived.log
fi


```


```shell
#------------138配置--------------
! Configuration File for keepalived

global_defs {
   router_id node201
}

vrrp_script check_run {
   script "/etc/keepalived/closekeepalived.sh"
   interval 2
}

vrrp_instance VI_1 {
    state MASTER
    interface enp2s0f1
    virtual_router_id 51
    priority 238
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
        }
    virtual_ipaddress {
        10.128.136.135
        }
    track_script {
        check_run
        }
}




#------------139配置--------------
! Configuration File for keepalived

global_defs {
   router_id node202
}

vrrp_script check_run {
   script "/etc/keepalived/closekeepalived.sh"
   interval 2
}

vrrp_instance VI_1 {
    state BACKUP
    interface enp2s0f1
    virtual_router_id 51
    priority 139
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
        }
    virtual_ipaddress {
        10.128.136.135
        }
    track_script {
        check_run
        }
}


```