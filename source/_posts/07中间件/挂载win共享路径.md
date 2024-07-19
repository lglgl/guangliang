[root@localhost ~]# mount -t cifs -o username=win10User,password=win10Pw,sec=ntlm //win10Ip/share /mnt/win10
或者
# mount -t cifs -o username=win10User,password=win10Pw //win10Ip/share /mnt/win10



//192.168.240.221/Users/liguangliang/Documents/运维/3.配置文件/ansible /etc/ansible cifs username=lglglgl@live.com,password=libng0510 0 0

//192.168.240.221/Users/liguangliang/Documents/运维/3.配置文件/docker-compose /data/docker-compose cifs username=lglglgl@live.com,password=libng0510 0 0

//192.168.240.221/Users/liguangliang/Documents/运维/3.配置文件/exporter-config /data/exporter-config cifs username=lglglgl@live.com,password=libng0510 0 0

-----------------

mount -t cifs -o username=lglglgl@live.com,password=13428155037 //192.168.240.222/Users/liguangliang/Documents/运维/3.配置文件/ansible /etc/ansible
&& 
mount -t cifs -o username=lglglgl@live.com,password=13428155037 //192.168.240.222/Users/liguangliang/Documents/运维/3.配置文件/docker-compose /data/docker-compose

mount -t cifs -o username=lglglgl@live.com,password=13428155037 //192.168.240.222/Users/liguangliang/Documents/运维/3.配置文件/exporter-config /data/exporter-config

/etc/ansible


凯哥：223
法老：221
空空：222



