## 概述
[官网地址](http://kafka.apache.org/)
### Kafka的几个概念
* producer：生产者
* consumer：消费者
* broker：数据存放
* topic：主题，给消息打标签

## 安装
### 单机版安装
```
# 依赖环境：Java+Zookeeper(不过Kafka中也内置了Zookeeper)

# 下载并解压
tar -xf kafka_2.11-2.0.1.tgz -C /app/

# 配置环境变量
vim /etc/profile
	export KAFKA_HOME=/app/kafka_2.11-2.0.1
	export PATH=$KAFKA_HOME/bin:$PATH

source /etc/profile

# 启动Kafka(这个时候直接启动会失败一定记得先启动Zookeeper)
zookeeper-server-start.sh -daemon /app/kafka_2.11-2.0.1/config/zookeeper.properties
kafka-server-start.sh -daemon /app/kafka_2.11-2.0.1/config/server.properties

# 查看是否启动成功
jps -m
```

![kafka-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Log%20Analysis/img/kafka-1.png)

### 集群版安装
针对Kafka集群，就是多个broker，在kafka的配置文件中可以进行查看：

vim /app/kafka_2.11-2.0.1/config/server.properties 

```
broker.id=0
```

所以要配置集群，有两种方式：

```
单节点多broker：此时只需要多复制几个server.properties文件并且修改其中的broker.id的值，多次启动kafka即可；
	kafka-server-start.sh -daemon /app/kafka_2.11-2.0.1/config/server1.properties
	kafka-server-start.sh -daemon /app/kafka_2.11-2.0.1/config/server2.properties

多节点多broker：在多台计算机上安装kafka并启动，需要注意broker.id的值应该是唯一的。
```

### Kafka配置文件
vim /app/kafka_2.11-2.0.1/config/server.properties

```
broker.id=0
listeners=PLAINTEXT://hostname:9092 # 真正bind的地址
# 如果kafka是在docker或者虚拟机中，外部访问时，需要使用以下参数
# advertised.listeners=PLAINTEXT://hostname:9092 # 暴露给外部的listeners
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400 # 发送缓存字节数
socket.receive.buffer.bytes=102400 # 接受缓存字节数
socket.request.max.bytes=104857600 # 保护OOM
log.dirs=/tmp/kafka-logs # 日志目录
num.partitions=1 # 默认分区数
num.recovery.threads.per.data.dir=1 # 
offsets.topic.replication.factor=1 # 
transaction.state.log.replication.factor=1 #
transaction.state.log.min.isr=1 #
log.retention.hours=168 # 日志保留时间
log.segment.bytes=1073741824 # 
log.retention.check.interval.ms=300000 # 
zookeeper.connect=localhost:2181 # zookeeper地址
zookeeper.connection.timeout.ms=6000 # Zookeeper连接超时时间
group.initial.rebalance.delay.ms=0 # 
```

## 使用
### Kafka常用命令
Note				| Command
---					| ---
创建topic			| kafka-topics.sh --zookeeper hostname:2181 --create --replication-factor 1 --partitions 1 --topic topic_name
列举topic			| kafka-topics.sh --zookeeper hostname:2181 --list
查看topic详情		| kafka-topics.sh --zookeeper hostname:2181 --describe --topic topic_name
修改topic分区信息	| kafka-topics.sh --zookeeper hostname:2181 --alter --partitions 20 --topic topic_name
删除topic			| kafka-topics.sh --zookeeper hostname:2181 --delete --topic topic_name
启动consumer		| kafka-console-consumer.sh --zookeeper hostname:2181 --topic topic_name
启动producer		| kafka-console-producer.sh --broker-list hostname:9092 --topic topic_name
从头消费			| --from-beginning

### 日志文件清理
在配置文件中进行修改

```
log.retention.hours=1
log.retention.minutes=60
log.retention.ms=3600000

或者

log.retention.bytes=1073741824
```

## 踩坑记
* 删除topic报错

```
Note: This will have no impact if delete.topic.enable is not set to true.
```

解决方案：

```
在server.properties添加delete.topic.enble=true
```

## 参考资料
