### 前置说明
nacos数据保存在MySQL，故直接配置运行即可。无单独data和config目录。


https://nacos.io/zh-cn/docs/quick-start-docker.html


```shell
#默认JVM配置
JVM_XMS	-Xms	默认 :1g
JVM_XMX	-Xmx	默认 :1g
JVM_XMN	-Xmn	默认 :512m
JVM_MS	-XX:MetaspaceSize	默认 :128m
JVM_MMS	-XX:MaxMetaspaceSize	默认 :320m

#设置年轻代大小为2G。整个JVM内存大小=年轻代大小xmn + 年老代大小 + 持久代大小。元空间与永久代最大的区别在于：元空间并不在虚拟机中，而是使用本机内存。
JVM_XMS	-Xms	4g
JVM_XMX	-Xmx	4g
JVM_XMN	-Xmn	2g
JVM_MS	-XX:MetaspaceSize	4g
JVM_MMS	-XX:MaxMetaspaceSize	4g

# docker配置
-e JVM_XMS=4g -e JVM_XMX=4g JVM_XMN=2g \
```
