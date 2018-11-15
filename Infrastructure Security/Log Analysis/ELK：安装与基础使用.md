## 概述
[官网](https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html)

## 安装
操作系统：Centos7
### 使用YUM安装ELK

```
# 导入私钥
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

# 添加yum源
vim /etc/yum.repos.d/es.repo
```

yum源内容：

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

执行安装：

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

![elk-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-1.png)

![elk-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-2.png)

### 使用RPM安装
```
# 安装java并配置环境变量

# 安装es
rpm -ivh elasticsearch-6.2.4.rpm

# 修改es的java环境变量
vim /etc/sysconfig/elasticsearch
    JAVA_HOME=/usr/share/jdk1.8.0_171
source /etc/sysconfig/elasticsearch

# 启动es

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

## 配置文件说明
### elasticsearch.yml
vim /etc/elasticsearch/elasticsearch.yml
 
```
cluster.name: my-application # 集群名称

node.name: node-1 # 节点名称

node.attr.rack: r1 # 节点属性

path.data: /var/lib/elasticsearch # 数据存储目录

path.logs: /var/log/elasticsearch # 日志存储目录

bootstrap.memory_lock: true # 内存锁定

network.host: 0.0.0.0 # 允许访问的主机

http.port: 9200 # 允许访问的端口

discovery.zen.ping.unicast.hosts: ["host1", "host2"] # 启动新节点时，传递初始主机列表用于发现主机

discovery.zen.minimum_master_nodes: # 配置节点数防止脑裂

gateway.recover_after_nodes: 3 # 在N个节点后阻塞恢复

action.destructive_requires_name: true # 删除索引需要显示名称
```

### logstash.yml
vim /etc/logstash/logstash.yml

```
node.name: test # 节点名称

path.data: /var/lib/logstash # 数据存储目录

path.logs: /var/log/logstash # 日志存储目录

pipeline.id: main # pipeline ID

pipeline.workers: 2 # 输出通道的worker数量，默认为CPU核数

pipeline.batch.size: 125 # 每次input数量

pipeline.batch.delay: 50 # 

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

### kibana.yml
vim /etc/kibana/kibana.yml

```
server.port: 5601 # 绑定端口

server.host: "0.0.0.0" # 绑定主机

server.basePath: "" # 

server.name: "your-hostname" # hostname名称

elasticsearch.url: "http://localhost:9200" # ElasticSearch地址
```

## 测试ELK
### 测试Logstash
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

![elk-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-2.png)
