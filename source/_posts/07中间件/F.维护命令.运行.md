



### 运行
```shell

docker-compose start

docker-compose down
# kafka运行
cd /home/idszt/docker-compose/middleware/kafka && docker-compose start


# 启动顺序MySQL、zookeeper
cd /home/idszt/docker-compose/middleware/zookeeper && docker-compose start

## 次级启动项
cd /home/idszt/docker-compose/middleware/elasticsearch && docker-compose up
cd /home/idszt/docker-compose/middleware/kafka && docker-compose start




```