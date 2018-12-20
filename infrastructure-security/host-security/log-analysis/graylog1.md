# Graylog日志管理系统
[官网地址](https://www.graylog.org/)

[官方文档](http://docs.graylog.org/)

## 安装与使用
### 安装graylog
* **解决依赖**

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

* **安装graylog**

```
rpm -Uvh https://packages.graylog2.org/repo/packages/graylog-2.4-repository_latest.rpm

yum install graylog-server -y
```

### 配置文件
```
vim /etc/graylog/server/server.conf
```

```
# 常规配置
is_master = true # 是否是主节点
password+secret = <secret> # 设置密钥

root_username = admin # 默认用户
root_password_sha2 = <SHA2> # 密码的SHA2 hash值

# REST API地址
rest_listen_uri = http://0.0.0.0:9000/api/

# Web地址
web_listen_uri = http://0.0.0.0:9000/

# ES地址
elasticsearch_hosts = http://node1:9200

# MongoDB配置
mongodb_uri = mongodb://localhost/graylog
```

生成密钥

```
yum install pwgen.x86_64 -y

pwgen -N 1 -s 96
```

生成密码的SHA2

```
echo -n "Enter Password: " && head -1 </dev/stdin | tr -d '\n' | sha256sum | cut -d" " -f1
```

启动服务

```
systemctl start graylog-server
```

## 配置录入数据
system/overview-inputs

![graylog-1](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-1.png)

* **配置rsyslog**

```
vim /etc/rsyslog.d/graylog.conf
```

graylog.conf文件内容

```
*.* @@10.10.10.5:5140;RSYSLOG_SyslogProtocol23Format
```

重启rsyslog服务

```
systemctl restart rsyslog.service
```

点击：show received messages

![graylog-2](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-2.png)

## 参考资料
[Sending syslog from Linux systems into Graylog](https://marketplace.graylog.org/addons/a47beb3b-0bd9-4792-a56a-33b27b567856)
