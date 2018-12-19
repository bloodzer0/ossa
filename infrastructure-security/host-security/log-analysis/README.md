# 日志分析
### Web日志分析目标
* 返回状态码分布图：针对Java出现大量500的情况需要注意；
* PV趋势图：
* 源IP分布图：
* 来源国家排行：
* 来源城市排行：
* 独立IP访问趋势图：
* 访问路径排行：
* 访问来源排行：
* 浏览器分布图：
* 特定接口访问图：

### 日志分析架构
我们企业内部使用的架构：

> 日志源-->rsyslog client-->rsyslog server-->kafka cluster-->ELK cluster

* **rsyslog client**

```
# 导入模块
module(load="imfile" mode="inotify")

# 设置工作目录
$WorkDirectory /var/lib/rsyslog

$ActionQueueWorkerThreads 1
$ActionQueueDequeueBatchSize 30
$ActionQueueDequeueSlowdown 300
$ActionQueueDiscardSeverity 8
$ActionQueueFileName action_queue_buffer_bitun_nginx_access
$ActionQueueMaxDiskSpace 10m
$ActionQueueSaveOnShutdown on

$MaxMessageSize 64k

input(type="imfile"
    File="log-path"
    Tag="tag_name"
    Severity="info"
    PersistStateInterval="20000"
    reopenOnTruncate="on"
    )

if $programname == 'tag_name' then @@Rsyslog Server IP:Port
if $programname == 'tag_name' then stop
```

* **rsyslog server**

```
module(load="omkafka")
$SystemLogSocketFlowControl on #如果队列满了，会暂停一会等待队列消耗
$SystemLogRateLimitInterval 0 #default 0 速率限制，改为0不限制
$ActionQueueType LinkedList   # use asynchronous processing
$ActionQueueFileName backup_local # set file name, also enables disk mode
$ActionResumeRetryCount -1    # infinite retries on insert failure
$ActionQueueSaveOnShutdown on
$ActionQueueMaxFileSize 500m
$ActionQueueMaxDiskSpace 10g

$template l7_msg,"%msg:1:$%"

:rawmsg, contains, "tag_name" action(
    type="omkafka"
    broker="Kafka Server IP:Port(默认9092)"
    topic="topic_name"
    partitions.number="10"
    confParam=[
        "socket.keepalive.enable=true"
    ]
    template="l7_msg"
    action.resumeretrycount="1"
)
```

* **kafka cluster**

```
# 创建好topic
```

* **Logstash**

```
input {
    kafka {
        bootstrap_servers => ["Kakfa Server IP:Port(默认9092)"]
        topics => ["topic_name"]
        type => "httpd"
    }
}
output {
    elasticsearch {
        hosts => ["ElasticSearch Server IP:Port(默认9200)"]
        index => "httpd-%{+YYYY.MM}"
    }
}
```

## 日志源

## 日志采集
### rsyslog
参考：日志分析架构一节

[官方文档](https://www.rsyslog.com/connecting-with-logstash-via-apache-kafka/)

### Beats
```
Filebeat：
Packetbeat：
Metricbeat：
Winlogbeat：
```

* **Beats安装**

[安装文档](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation.html)

* **Beats配置**

[Input 配置](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-filebeat-options.html)

[Output 配置](https://www.elastic.co/guide/en/beats/filebeat/current/configuring-output.html)

* **Beats案例**

### Flume
* source：
* channel：
* sink：

* **Flume安装**

```
# 解决依赖
tar -xf jdk-8u191-linux-x64.tar.gz -C /app/
vim /etc/profile
	export JAVA_HOME=/app/jdk1.8.0_191
	export PATH=$JAVA_HOME/bin:$PATH

source /etc/profile

# 验证java是否安装成功
java -version

# 下载flume，解压到指定目录
tar -xf apache-flume-1.8.0-bin.tar.gz -C /app/

# 配置环境变量
vim /etc/profile
```

/etc/profile文件内容：

```
export FLUME_HOME=/app/apache-flume-1.8.0-bin
export PATH=$FLUME_HOME/bin:$PATH
```

```
source /etc/profile

# 配置flume运行环境文件
cp /app/apache-flume-1.8.0-bin/conf/flume-env.sh.template /app/apache-flume-1.8.0-bin/conf/flume-env.sh
vim /app/apache-flume-1.8.0-bin/conf/flume-env.sh
```

flume-env.sh文件内容：

```
export JAVA_HOME=JAVA_PATH
```

```
# 查看是否安装完成
flume-ng version
```

* **Flume案例：从指定网络端口采集数据输出到控制台**

[官方文档](http://flume.apache.org/FlumeUserGuide.html#a-simple-example)

创建配置文件：

```
vim /app/apache-flume-1.8.0-bin/conf/example-1.conf
```

example-1.conf文件内容：

```
# a1:agent名称
# r1:source名称
# c1:channel名称
# k1:sink名称

# Name the components on this agent
a1.sources = r1
a1.channels = c1
a1.sinks = k1

# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

执行命令：

```
flume-ng agent --name a1 --conf /app/apache-flume-1.8.0-bin/conf \ 
    --conf-file /app/apache-flume-1.8.0-bin/conf/example-1.conf \ 
    -Dflume.root.logger=INFO,console

# 备注：-Dflume.root.logger=INFO,console（java参数，INFO输出到控制台）
```

对输出结果的说明：

```
Event是Flume数据传输的基本单元，Event=[header(可选)] + <body(必选)>
```

* **Flume案例：实时监控文件新增数据输出到控制台**

创建配置文件：

```
vim /app/apache-flume-1.8.0-bin/conf/example-2.conf
```

example-2.conf文件内容：

```
# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /var/log/messages
a1.sources.r1.shell = /bin/sh -c

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

执行命令：

```
flume-ng agent --name a1 --conf /app/apache-flume-1.8.0-bin/conf \ 
    --conf-file /app/apache-flume-1.8.0-bin/conf/example-2.conf \ 
    -Dflume.root.logger=INFO,console
```

* **Flume案例：将Server1的日志传输到Server2的控制台**

在sever1创建配置文件：

```
vim /app/apache-flume-1.8.0-bin/conf/exec-memory-avro.conf
```

exec-memory-avro.conf文件内容：

```
exec-memory-avro.sources = exec-source
exec-memory-avro.sinks = avro-sink
exec-memory-avro.channels = memory-channel

exec-memory-avro.sources.exec-source.type = exec
exec-memory-avro.sources.exec-source.command = tail -F /var/log/messages
exec-memory-avro.sources.exec-source.shell = /bin/sh -c

exec-memory-avro.sinks.avro-sink.type = avro
exec-memory-avro.sinks.avro-sink.hostname = localhost
exec-memory-avro.sinks.avro-sink.port = 44444

exec-memory-avro.channels.memory-channel.type = memory
exec-memory-avro.channels.memory-channel.capacity = 1000
exec-memory-avro.channels.memory-channel.transactionCapacity = 100

exec-memory-avro.sources.exec-source.channels = memory-channel
exec-memory-avro.sinks.avro-sink.channel = memory-channel
```

在server2创建配置文件：

```
vim /app/apache-flume-1.8.0-bin/conf/avro-memory-logger.conf
```

avro-memory-logger.conf文件内容：

```
avro-memory-logger.sources = avro-source
avro-memory-logger.sinks = logger-sink
avro-memory-logger.channels = memory-channel

avro-memory-logger.sources.avro-source.type = avro
avro-memory-logger.sources.avro-source.bind = localhost
avro-memory-logger.sources.avro-source.port = 44444

avro-memory-logger.sinks.logger-sink.type = logger

avro-memory-logger.channels.memory-channel.type = memory
avro-memory-logger.channels.memory-channel.capacity = 1000
avro-memory-logger.channels.memory-channel.transactionCapacity = 100

avro-memory-logger.sources.avro-source.channels = memory-channel
avro-memory-logger.sinks.logger-sink.channel = memory-channel
```

执行命令：**先在server2执行，再执行server1**

```
flume-ng agent --name avro-memory-logger --conf /app/apache-flume-1.8.0-bin/conf \ 
    --conf-file /app/apache-flume-1.8.0-bin/conf/avro-memory-logger.conf \ 
    -Dflume.root.logger=INFO,console

flume-ng agent --name exec-memory-avro --conf /app/apache-flume-1.8.0-bin/conf \ 
    --conf-file /app/apache-flume-1.8.0-bin/conf/exec-memory-avro.conf \ 
    -Dflume.root.logger=INFO,console
```

### Logstash
[官方文档](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## 日志处理
### Logstash filter

### Spark or Storm

## 日志存储
### Database

### ElasticSearch

### Hive

### HBase

## 可视化展示(日志价值)

### Kibana

### Graylog

## 日志分析平台对比

## 参考资料
[几十条业务线日志系统如何收集处理？](https://www.cnblogs.com/zhangs1986/p/6897200.html)

[Splunk vs ELK](https://www.upguard.com/articles/splunk-vs-elk)
