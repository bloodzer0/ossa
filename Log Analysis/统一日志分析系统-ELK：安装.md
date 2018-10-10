[官网地址](https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html)

备注：操作系统Centos7

# 1.使用yum直接安装
```
# 导入私钥
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

# 添加yum源
vim /etc/yum.repos.d/es.repo

[elasticsearch-6.x]
name=Elasticsearch repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md

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

# 2.使用rpm安装
```
# 安装java并配置环境变量

# 安装es
rpm -ivh elasticsearch-6.2.4.rpm

# 修改es的java环境变量
vim /etc/sysconfig/elasticsearch
    JAVA_HOME=/usr/share/jdk1.8.0_171
source /etc/sysconfig/elasticsearch

# 启动es
systemctl start elasticsearch.service

# 安装kibana
rpm -ivh kibana-6.2.4-x86_64.rpm

# 创建java软链接
ln -s /usr/share/jdk1.8.0_171/bin/java /usr/bin/java

# 安装logstash
rpm -ivh logstash-6.2.4.rpm
```
