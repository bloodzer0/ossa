[官网地址](http://wazuh.com/)

[官方文档](https://documentation.wazuh.com/current/index.html)

# server端
```
# java环境
tar -xf jdk-8u171-linux-x64.tar.gz -C /usr/share/
vim /etc/profile
source /etc/profile
ln -s /usr/share/jdk1.8.0_171/bin/java /usr/sbin/java # 为了解决logstash登录

# es安装
rpm -ivh elasticsearch-6.3.0.rpm 

# 配置ES，修改host
vim /etc/elasticsearch/elasticsearch.yml

# 解决es java环境问题
vim /etc/sysconfig/elasticsearch
source /etc/sysconfig/elasticsearch 

# 启动es
systemctl enable elasticsearch.service
systemctl start elasticsearch.service

# 导入es template
curl https://raw.githubusercontent.com/wazuh/wazuh/3.3/extensions/elasticsearch/wazuh-elastic6-template-alerts.json | curl -XPUT 'http://localhost:9200/_template/wazuh' -H 'Content-Type: application/json' -d @-

# 安装kibana
rpm -ivh kibana-6.3.0-x86_64.rpm 

# 配置kibana与启动kibana
vim /etc/kibana/kibana.yml
systemctl enable kibana.service
systemctl start kibana.service

# 安装插件
/usr/share/kibana/bin/kibana-plugin install file:///root/wazuhapp-3.3.1_6.3.0.zip

# logstash安装
rpm -ivh logstash-6.3.0.rpm

# 配置logstash：见文末
vim /etc/logstash/conf.d/wazuh.conf

# 启动logstash
systemctl enable logstash.service
systemctl start logstash.service

# 安装filebeat
rpm -ivh filebeat-6.3.0-x86_64.rpm

# 配置Filebeat：见文末
vim /etc/filebeat/filebeat.yml 

# 启动filebeat
systemctl enable filebeat.service
systemctl start filebeat.service

# 安装wazuh server
rpm -ivh wazuh-manager-3.3.1-1.x86_64.rpm

# 安装wazuh-api
wget -qO- https://rpm.nodesource.com/setup_10.x | bash - # 配置nodejs源
yum install gcc-c++ make yarn nodejs.x86_64 -y # 安装nodejs
rpm -ivh wazuh-api-3.3.1-1.x86_64.rpm
systemctl status wazuh-api.service

# 添加api用户
cd /var/ossec/api/configuration/auth
./htpasswd -c user bloodzer0
systemctl restart wazuh-api.service
```

# agent端
```
# 安装agent
rpm -ivh wazuh-agent-3.3.1-1.x86_64.rpm

# 修改配置文件
vim /var/ossec/etc/ossec.conf

# 导入密钥
/var/ossec/bin/manage_agents

# 启动服务
/var/ossec/bin/ossec-control start
```


* logstash-wazuh.conf

```
input {
    beats {
        port => 5044
        codec => "json_lines"
    }
}
filter {
    if [data][srcip] {
        mutate {
            add_field => [ "@src_ip", "%{[data][srcip]}" ]
        }
    }
    if [data][aws][sourceIPAddress] {
        mutate {
            add_field => [ "@src_ip", "%{[data][aws][sourceIPAddress]}" ]
        }
    }
}
filter {
    geoip {
        source => "@src_ip"
        target => "GeoLocation"
        fields => ["city_name", "country_name", "region_name", "location"]
    }
    date {
        match => ["timestamp", "ISO8601"]
        target => "@timestamp"
    }
    mutate {
        remove_field => [ "timestamp", "beat", "input_type", "tags", "count", "@version", "log", "offset", "type", "@src_ip", "host"]
    }
}
output {
    elasticsearch {
        hosts => ["localhost:9200"]
        index => "wazuh-alerts-3.x-%{+YYYY.MM.dd}"
        document_type => "wazuh"
    }
}
```

* filebeat-filebeat.yml

```
filebeat.inputs:
- type: log
  paths:
    - "/var/ossec/logs/alerts/alerts.json"
  document_type: json
  json.message_key: log
  json.keys_under_root: true
  json.overwrite_keys: true

output.logstash:
  hosts: ["localhost:5044"]
```
