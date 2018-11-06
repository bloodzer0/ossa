## 概述
[原文地址](https://jordanpotti.com/2018/02/16/elk-osquery-kolide-fleet-love/)

* Osquery：基于SQL的主机管理工具；

* Kolide Fleet：用于控制Osquery列表的灵活服务器，允许我们按需查询多个主机，以及创建查询包，构建查询计划与管理我们的主机；

* Elastic Stack：ELK允许收集、处理和可视化日志的工具；

## 安装
操作系统：Centos7，3款软件都在同一服务器上安装。

**默认Osquery与ELK已经安装完成，本篇中不赘述它们的安装过程**
### 安装Kolide Fleet
[官方安装地址](https://github.com/kolide/fleet/blob/master/docs/infrastructure/fleet-on-ubuntu.md)

* 安装MySQL

```
# 下载yum源
wget https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm

# 安装yum源
rpm -ivh mysql80-community-release-el7-1.noarch.rpm

# 安装数据库
yum install mysql-community-server.x86_64 mysql-community-client.x86_64 -y

# 创建数据库
create database kolide;
```

* 安装Redis

```
# 安装源
yum install epel-release -y

# 安装redis
yum install redis.x86_64 -y

# 启动服务
systemctl start redis.service
```

* 安装Fleet

```
# 下载
wget https://dl.kolide.co/bin/fleet_latest.zip

# 解压
unzip fleet_latest.zip 'linux/*' -d fleet

# 拷贝执行命令到bin目录
cp fleet/linux/fleet /usr/bin/fleet
cp fleet/linux/fleetctl /usr/bin/fleetctl
```

* 准备数据库

```
# 密码没什么用啊！一次性密码
/usr/bin/fleet prepare db --mysql_address=127.0.0.1:3306 --mysql_database=kolide --mysql_username=root --mysql_password=BloodZer@0

# 成功如下图：Migrations completed
```

![osquery-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-2.png)

* 配置证书

```
openssl genrsa -out /etc/pki/tls/private/server.key 4096

openssl req -new -key /etc/pki/tls/private/server.key -out /etc/pki/tls/certs/server.csr

openssl x509 -req -days 366 -in /etc/pki/tls/certs/server.csr -signkey /etc/pki/tls/private/server.key -out /etc/pki/tls/certs/server.cert
```

* 再次启动Fleet Server

```
# 创建日志目录
mkdir /var/log/kolide

# 启动服务
/usr/bin/fleet serve \
    --mysql_address=127.0.0.1:3306 \
    --mysql_database=kolide \
    --mysql_username=root \
    --mysql_password=BloodZer@0 \
    --redis_address=127.0.0.1:6379 \
    --server_cert=/etc/pki/tls/certs/server.cert \
    --server_key=/etc/pki/tls/private/server.key \
    --logging_json \
    --osquery_result_log_file=/var/log/kolide/osquery_result \
    --osquery_status_log_file=/var/log/kolide/osquery_status

# 这个时候会再次报错如下图
```

![osquery-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-3.png)

我们使用它推荐的--auth_jwt_key

```
/usr/bin/fleet serve \
    --mysql_address=127.0.0.1:3306 \
    --mysql_database=kolide \
    --mysql_username=root \
    --mysql_password=BloodZer@0 \
    --redis_address=127.0.0.1:6379 \
    --server_cert=/etc/pki/tls/certs/server.cert \
    --server_key=/etc/pki/tls/private/server.key \
    --logging_json \
    --osquery_result_log_file=/var/log/kolide/osquery_result \
    --osquery_status_log_file=/var/log/kolide/osquery_status \
    --auth_jwt_key=lGwR9p27shKFwIX+q0+15YbYzpK6KdLH

# 成功如下图
```

![osquery-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-4.png)

* 访问https://hostname:8080进行安装 

![osquery-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-5.png)

安装成功，界面如图

![osquery-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-6.png)

### 推送主机到Fleet Server
[官方文档](https://github.com/kolide/fleet/blob/master/docs/infrastructure/adding-hosts-to-fleet.md)

* 利用Fleet Launcher推送主机

```
# 下载
wget https://github.com/kolide/launcher/releases/download/0.5.0/launcher_0.5.0.zip

# 解压
unzip launcher_0.5.0.zip

# 执行，最简单的模式
cd linux/
./launcher --hostname=localhost:8080 --enroll_secret=RYUmaE35ESfKWxzLlWxk/HsW95O5AXpT --insecure

# 执行完成以后，在Web界面能看到新增的主机

```

![osquery-7](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-7.png)

关于Launcher的参数解释：

```
--hostname:Fleet Server主机的地址
--enroll_secret:Fleet Server的密钥，密钥地址如图
--insecure:无效的CA证书需要使用此参数
```

![osquery-8](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-8.png)

* 利用Osquery推送主机

```
# 创建密钥
echo 'RYUmaE35ESfKWxzLlWxk/HsW95O5AXpT' > /var/osquery/enroll_secret

# 配置证书，证书下载位置如图，下载完成后进行改名
mv localhost.pem /var/osquery/server.pem

# 执行osqueryd
/usr/bin/osqueryd \
    --enroll_secret_path=/var/osquery/enroll_secret \
    --tls_server_certs=/var/osquery/server.pem \
    --tls_hostname=localhost:8080 \
    --host_identifier=hostname \
    --enroll_tls_endpoint=/api/v1/osquery/enroll \
    --config_plugin=tls \
    --config_tls_endpoint=/api/v1/osquery/config \
    --config_tls_refresh=10 \
    --disable_distributed=false \
    --distributed_plugin=tls \
    --distributed_interval=3 \
    --distributed_tls_max_attempts=3 \
    --distributed_tls_read_endpoint=/api/v1/osquery/distributed/read \
    --distributed_tls_write_endpoint=/api/v1/osquery/distributed/write \
    --logger_plugin=tls \
    --logger_tls_endpoint=/api/v1/osquery/log \
    --logger_tls_period=10
```

![osquery-9](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-9.png)

## 使用
### 利用ELK收集日志进行处理展示
* 创建一个查询

![osquery-10](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-10.png)

* 创建一个pack

![osquery-11](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-11.png)

* 查看日志

```
tail -f /var/log/kolide/osquery_result
```

![osquery-12](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-12.png)

* 利用ELK解析并展示日志

vim /etc/logstash/conf.d/fleet.conf

```
input {
    file {
        path => ["/var/log/kolide/osquery_result"]
        codec => "json"
    }
}

output {
    elasticsearch {
        hosts => ["127.0.0.1:9200"]
        index => "logstash-fleet-%{+YYYY.MM.dd}"
    }
}
```

配置好ELK，查看展示图

![osquery-13](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-13.png)

## 参考资料
