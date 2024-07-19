### 版本
filebeat-8.8.1-linux-x86_64

zookeeper:3.7.1
wurstmeister/kafka:2.13-2.8.1

elasticsearch:7.15.2
logstash:7.15.2
kibana:7.15.2





kibana-7.15.2.tar
docker load < kibana-7.15.2.tar && docker tag abca6a9e3cf8 docker.elastic.co/kibana/kibana:7.15.2



docker load < logstash-7.15.2.tar
docker tag 6bd1bad7c9c6 docker.elastic.co/logstash/logstash:7.15.2



docker pull docker.elastic.co/beats/filebeat:7.15.2