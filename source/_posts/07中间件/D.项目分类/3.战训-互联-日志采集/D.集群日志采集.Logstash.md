 
 
 
 
 
 mkdir -p /docker_data/loganalysis_logstash/config
 
 
 prod_loganalysis_kibana_02
 
docker run  --name prod_loganalysis_logstash_1 \
 --network=host --privileged=true --restart=always \
 -v /docker_data/loganalysis_logstash/config:/usr/share/logstash/config \
 -e ES_JAVA_OPTS="-Xms1024m -Xmx1024m" \
 docker.elastic.co/logstash/logstash:7.15.2



docker run  --name prod_loganalysis_logstash_2 \
 --network=host --privileged=true --restart=always \
 -v /docker_data/loganalysis_logstash/config:/usr/share/logstash/config \
 -e ES_JAVA_OPTS="-Xms1024m -Xmx1024m" \
 docker.elastic.co/logstash/logstash:7.15.2

 http://192.168.10.188:9200/_cat/indice