









[root@ansible ~]# wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.15.2-x86_64.rpm
--2023-08-04 04:09:08--  https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.15.2-x86_64.rpm
Resolving artifacts.elastic.co (artifacts.elastic.co)... 34.120.127.130, 2600:1901:0:1d7::
Connecting to artifacts.elastic.co (artifacts.elastic.co)|34.120.127.130|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 35555181 (34M) [binary/octet-stream]
Saving to: ‘filebeat-7.15.2-x86_64.rpm’

100%[========================================================================================================================================>] 35,555,181  1.89MB/s   in 24s

2023-08-04 04:09:33 (1.39 MB/s) - ‘filebeat-7.15.2-x86_64.rpm’ saved [35555181/35555181]

[root@ansible ~]# ls
anaconda-ks.cfg  docker_image       Documents  filebeat-7.15.2-x86_64.rpm  original-ks.cfg  Public    Templates
Desktop          docker-prometheus  Downloads  Music                       Pictures         software  Videos
[root@ansible ~]# rpm -ivh filebeat-7.15.2-x86_64.rpm
warning: filebeat-7.15.2-x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID d88e42b4: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:filebeat-7.15.2-1                ################################# [100%]
[root@ansible ~]#



systemctl start filebeat
设置开机启动：

systemctl enable filebeat
检查 Filebeat 启动状态：

systemctl status filebeat


vi /etc/filebeat/filebeat.yml

cat /etc/filebeat/filebeat.yml