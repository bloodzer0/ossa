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
