## 概述
### 日志分析的几个板块
* 数据源

```
各类型的日志
```

* 数据采集

```
Rsyslog
Flume
Beats
Logstash
```

* 数据处理

```
Logstash
Spark
Storm
```

* 数据存储

```
ElasticSearch
Hive
HBase
```

* 可视化展示

```
Kibana
Graylog
```
## 企业自建日志分析平台
### 日志分析平台架构
在第一节中已经简单阐述过几种架构了，这里我说一下我自己用到的架构：

![log-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/log-1.png)

配置文件如下：

* Ryslog Client

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

* Ryslog Server

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

* kafka：创建好topic即可

* Logstash

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

## 参考资料
[几十条业务线日志系统如何收集处理？](https://www.cnblogs.com/zhangs1986/p/6897200.html)
