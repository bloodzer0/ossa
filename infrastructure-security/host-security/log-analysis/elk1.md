# ELK日志分析系统
[官网地址](https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html)

* **ElasticSearch**：是实时全文搜索和分析引擎，提供搜集、分析、存储数据三大功能；是一套开放REST和JAVA API等结构提供高效搜索功能，可扩展的分布式系统。它构建于Apache Lucene搜索引擎库之上。

* **Logstash**：是一个用来搜集、分析、过滤日志的工具。它支持几乎任何类型的日志，包括系统日志、错误日志和自定义应用程序日志。它可以从许多来源接收日志，这些来源包括syslog、消息传递（例如：RabbitMQ）和JMX，它能够以多种方式输出数据，包括电子邮件、WebSocket和ElasticSearch等。

* **Kibana**：是一个基于Web的图形界面，用于搜索、分析和可视化存储在ElasticSearch指标中的日志数据。它利用Elasticsearch的REST接口来检索数据，不仅允许用户创建他们自己的数据的定制仪表板视图，还允许他们以特殊的方式查询和过滤数据。

## 架构分析
### ELK基础架构
概述：由Logstash分布于各个节点上搜集相关日志、数据，并经过分析、过滤后发送给远端服务器上的Elasticsearch进行存储。Elasticsearch将数据以分片的形式压缩存储并提供多种API供用户查询，操作。

优点：搭建简单、易于上手。

缺点：Logstash消耗资源较大，运行占用CPU和内存高，没有消息队列缓存，存储数据丢失隐患。

适用场景：小规模集群或者学习者使用

### 引入消息队列
概述：引入消息队列机制，位于各个节点上的Logstash Agent先将数据传递到Kafka或Redis，并将队列中消息或数据间接传递给Logstash Server，Logstash Server分析、过滤后将数据传递给ES存储。

优点：引入消息队列机制，即使Logstash Server因故障停止运行，数据将会先被消息队列存储下来，从而避免数据丢失

缺点：由于Logstash中心节点和ES的负荷会比较重，可将它们配置为集群模式，以分担负荷

适用场景：较大集群的解决方案

### 引入Logstash-Forwarder
概述：引入Logstash-Forwarder，首先Logstash-Forwarder将日志数据统一发送给Logstash，经过分析、过滤后发送到ES进行存储。

优点：解决了Logstash在各机器上占用系统资源较高的问题，Logstash-Forwarder与Logstash之间的通信是通过SSL加密传输，起到安全保障

缺点：由于SSL加密传输导致的限制性

### 引入Beats

## 安装与使用
### yum安装ELK

```
# 导入私钥
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

# 添加yum源
vim /etc/yum.repos.d/es.repo
```

es.repo文件内容：

```
[elasticsearch-6.x]
name=Elasticsearch repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

```
# 安装java + ELK
yum install java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64 -y

yum install elasticsearch.noarch kibana.x86_64 logstash.noarch -y

# 修改配置文件
vim /etc/logstash/logstash.yml
vim /etc/elasticsearch/elasticsearch.yml
vim /etc/kibana/kibana.yml

# 启动服务
systemctl start logstash.service elasticsearch.service kibana.service

# 查看状态
systemctl status logstash.service elasticsearch.service kibana.service
```

### rpm安装ELK
```
# 安装java并配置环境变量

# 安装es
rpm -ivh elasticsearch-6.2.4.rpm

# 修改es的java环境变量
vim /etc/sysconfig/elasticsearch
    JAVA_HOME=/usr/share/jdk1.8.0_171
source /etc/sysconfig/elasticsearch

# 安装kibana
rpm -ivh kibana-6.2.4-x86_64.rpm

# 安装logstash
# 创建java软链接
ln -s /usr/share/jdk1.8.0_171/bin/java /usr/bin/java
rpm -ivh logstash-6.2.4.rpm
```

### 更新ELK
```
# 停止服务
systemctl stop logstash.service
systemctl stop kibana.service
systemctl stop elasticsearch.service

# 更新
yum update elasticsearch.noarch -y
yum update logstash.noarch -y
yum update kibana.x86_64 -y

# 重新加载
systemctl daemon-reload

# 启动服务
systemctl start elasticsearch.service logstash.service kibana.service
systemctl status elasticsearch.service logstash.service kibana.service
```

### 配置文件说明
* **elasticsearch.yml**

```
cluster.name: my-application # 集群名称
node.name: node-1 # 节点名称
node.attr.rack: r1 # 节点属性
path.data: /var/lib/elasticsearch # 数据存储目录
path.logs: /var/log/elasticsearch # 日志存储目录
bootstrap.memory_lock: true # 内存锁定，禁用虚拟内存
network.host: 0.0.0.0 # 绑定主机
http.port: 9200 # 绑定端口
discovery.zen.ping.unicast.hosts: ["host1", "host2"] # 启动新节点时，传递初始主机列表用于发现主机
discovery.zen.minimum_master_nodes: # 配置节点数防止脑裂
gateway.recover_after_nodes: 3 # 在N个节点后阻塞恢复
action.destructive_requires_name: true # 删除索引需要显示名称
```

* **logstash.yml**

```
node.name: test # 节点名称
path.data: /var/lib/logstash # 数据存储目录
path.logs: /var/log/logstash # 日志存储目录
pipeline.id: main # pipeline ID
pipeline.workers: 2 # 输出通道的worker数量，默认为CPU核数
pipeline.batch.size: 125 # 设定批处理数据的大小
pipeline.batch.delay: 50 # 设定批处理数据的延迟
pipeline.unsafe_shutdown: false # 
path.config: # 过滤配置文件目录
path.plugins: # 自定义插件目录
config.string: # 
config.test_and_exit: false # 
config.reload.automatic: false # 自动重载被修改的配置文件
config.reload.interval: 3s # 配置文件检查时间
http.host # 绑定主机
http.port # 绑定端口
log.level: # 日志输出级别，如果confie.dubug开启，这里一定要匹配debug
config.debug: false # 
log.format # 日志格式
config.support_escapes: false # 
```

* **kibana.yml**

```
server.port: 5601 # 绑定端口
server.host: "0.0.0.0" # 绑定主机
server.basePath: "" # 
server.name: "your-hostname" # hostname名称
elasticsearch.url: "http://localhost:9200" # ElasticSearch地址
```

### ELK测试
* **logstash配置文件**

```
input {
    file {
        path => ["/var/log/httpd/access_log"]
        type => "httpd"
    }
}

filter {
    grok {
        match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
}

output {
    elasticsearch {
        hosts => ["127.0.0.1:9200"]
        index => "logstash-httpd-%{+YYYY.MM.dd}"
    }
}
```

## 使用心得
### 适用场景
用于进行日志分析：包括用户行为日志、Web日志、服务器安全日志、防火墙或其他安全设备日志等。

当我们需要解决如下问题的时候，都是ELK的适用场景：

```
日志收集、处理、分析、展示
程序运行问题排查
服务器性能监控、应用监控、安全监控、错误报警
用户行为分析、漏洞分析等等
```

### 优点
* 强大的搜索功能：ElasticSearch支持DSL的语法来进行快速搜索，并且支持分布式检索方式。
* 支持分布式扩展：ElasticSearch与Logstash都支持分布式，可以进行横向扩展，能解决大型集群的很多问题。
* 完善的展示功能：Kibana支持非常详细的图表信息，同时也可以定制展示内容，实现高效的数据可视化。
* 简单的基础配置：学习成本低，能够满足查询秒级响应。

### 缺点
* 需要投入人力进行运营：运营成本。
* 数据的安全性保障较低，因为针对认证的Xpack模块是收费的，传输安全可以通过配置SSL来保障。
* 数据关联分析效果较差：目前在ELK中实现关联分析是比较难的。

## 参考资料
[ELK简介及架构分析](https://blog.csdn.net/m0_37814112/article/details/78717851)

[搭建安全认证的ELK日志系统](https://www.freebuf.com/articles/security-management/179736.html)
