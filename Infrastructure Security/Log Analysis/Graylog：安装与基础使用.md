## 概述
[官网地址](https://www.graylog.org/)

[官方文档](http://docs.graylog.org/)

## 安装
### 解决依赖
```
# java>=8
yum install java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64 -y

# MongoDB>=2.4
yum install epel-release -y
yum install mongodb.x86_64 mongodb-server.x86_64 -y

# 启动MongoDB
systemctl start mongod.service

# ElasticSearch>=2.x and <= 6.x
rpm -ivh elasticsearch-5.6.13.rpm

# 启动ElasticSearch
systemctl start elasticsearch.service 
```

### 安装graylog
```
rpm -Uvh https://packages.graylog2.org/repo/packages/graylog-2.4-repository_latest.rpm

yum install graylog-server -y
```

## 使用
### 配置文件
vim /etc/graylog/server/server.conf

```
# 常规配置
is_master = true # 是否是主节点
password+secret = <secret> # 设置密钥
	# 生成方式
	pwgen -N 1 -s 96

root_username = admin # 默认用户
root_password_sha2 = <SHA2> # 密码的SHA2 hash值
	# 生成方式
	echo -n "Enter Password: " && head -1 </dev/stdin | tr -d '\n' | sha256sum | cut -d" " -f1
	
rest_listen_uri = http://127.0.0.1:9000/api/ # REST API地址

# web配置
web_listen_uri = http://127.0.0.1:9000/  # Web地址
web_endpoint_uri =

# ElasticSearch配置
elasticsearch_hosts = http://node1:9200,http://user:password@node2:19200 # ES地址

# MongoDB配置
mongodb_uri = mongodb://localhost/graylog

# 启动服务
systemctl start graylog-server
```

![graylog-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-1.png)

### 配置输入
Web界面：System -> Inputs

![graylog-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-2.png)

配置rsyslog：vim /etc/rsyslog.d/graylog.conf

```
*.* @@192.168.199.5:5140 # 这里端口需要与界面配置的端口一致
```

查看效果

![graylog-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-3.png)

优化配置：vim /etc/rsyslog.d/graylog.conf

[原文地址](https://marketplace.graylog.org/addons/a47beb3b-0bd9-4792-a56a-33b27b567856)

```
*.* @@192.168.199.5:5141;RSYSLOG_SyslogProtocol23Format
```

## 参考资料
