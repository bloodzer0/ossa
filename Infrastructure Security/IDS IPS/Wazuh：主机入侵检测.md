## 概述
[官网地址](http://wazuh.com/)

[官方文档](https://documentation.wazuh.com/current/index.html)

## 安装
### Wazuh Server安装
```
rpm -ivh wazuh-manager-3.3.1-1.x86_64.rpm
systemctl status wazuh-manager.service
```

### Wazuh API安装
* 安装nodejs

```
wget -qO- https://rpm.nodesource.com/setup_8.x | bash - # 这里的版本会导致后面的报错

yum install nodejs.x86_64
```

* 安装python2（Centos7默认python2）

* 安装wazuh api

```
rpm -ivh wazuh-api-3.3.1-1.x86_64.rpm
systemctl status wazuh-api.service
```

### 安装ELK
* 安装ElasticSearch

```
# 安装java
yum install java-1.8.0-openjdk.x86_64 -y

# 安装es
rpm -ivh elasticsearch-6.3.0.rpm

systemctl start elasticsearch.service

# 创建template
curl https://raw.githubusercontent.com/wazuh/wazuh/3.3/extensions/elasticsearch/wazuh-elastic6-template-alerts.json | curl -XPUT 'http://localhost:9200/_template/wazuh' -H 'Content-Type: application/json' -d @-
```

* 安装Logstash

```
# 安装Logstash
ln -s /usr/share/jdk1.8.0_171/bin/java /usr/sbin/java
rpm -ivh logstash-6.3.0.rpm

# Logstash配置文件
wget https://raw.githubusercontent.com/wazuh/wazuh/3.3/extensions/logstash/01-wazuh-local.conf -O /etc/logstash/conf.d/01-wazuh.conf 

systemctl start logstash.service
```

* 安装Kibana

```
rpm -ivh kibana-6.3.0-x86_64.rpm

# 安装插件
export NODE_OPTIONS="--max-old-space-size=3072"
/usr/share/kibana/bin/kibana-plugin install https://packages.wazuh.com/wazuhapp/wazuhapp-3.3.1_6.3.0.zip

#卸载插件
/usr/share/kibana/bin/kibana-plugin remove wazuh
```

**备注：安装Kibana插件会报错**

```
DeprecationWarning: os.tmpDir() is deprecated. Use os.tmpdir() instead.
```

**解决方案：**

```
使用nodejs版本10
```

### 配置Wazuh API
```
cd /var/ossec/api/configuration/auth
./htpasswd -c user bloodzer0

#重启wazuh-api服务
```

## 使用
