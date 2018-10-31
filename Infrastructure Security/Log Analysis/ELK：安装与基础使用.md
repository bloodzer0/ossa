## 概述
[官网](https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html)

## 安装
操作系统：Centos7


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
yum install java-1.8.0-openjdk.x86_64 elasticsearch.noarch kibana.x86_64 logstash.noarch -y

# 修改配置文件
vim /etc/logstash/logstash.yml
vim /etc/elasticsearch/elasticsearch.yml
vim /etc/kibana/kibana.yml

# 启动服务
systemctl start logstash.service elasticsearch.service kibana.service

# 查看状态
systemctl status logstash.service elasticsearch.service kibana.service
```
