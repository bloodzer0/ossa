# ELK架构
## ElasticSearch+Logstash+Kibana
* 概述：由Logstash分布于各个节点上搜集相关日志、数据，并经过分析、过滤后发送给远端服务器上的ES进行存储。ES将数据以分片的形式压缩存储并提供多种API供用户查询，操作。
* 优点：搭建简单、易于上手
* 缺点：Logstash消耗资源较大，运行占用CPU和内存高，没有消息队列缓存，存储数据丢失隐患
* 适用场景：小规模集群或者学习者使用

## 引入消息队列
* 概述：引入消息队列机制，位于各个节点上的Logstash Agent先将数据传递到Kafka或Redis，并将队列中消息或数据间接传递给Logstash Server，Logstash Server分析、过滤后将数据传递给ES存储。
* 优点：引入消息队列机制，即使Logstash Server因故障停止运行，数据将会先被消息队列存储下来，从而避免数据丢失
* 缺点：由于Logstash中心节点和ES的负荷会比较重，可将它们配置为集群模式，以分担负荷
* 适用场景：较大集群的解决方案

## 引入Logstash-Forwarder
* 概述：引入Logstash-Forwarder，首先Logstash-Forwarder将日志数据统一发送给Logstash，经过分析、过滤后发送到ES进行存储。
* 优点：解决了Logstash在各机器上占用系统资源较高的问题，Logstash-Forwarder与Logstash之间的通信是通过SSL加密传输，起到安全保障
* 缺点：由于SSL加密传输导致的限制性

## 引入Beats
