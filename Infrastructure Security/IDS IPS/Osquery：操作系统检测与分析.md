## 概述
[官网地址](https://osquery.io/)

## 安装
[官方安装文档](https://osquery.readthedocs.io/en/stable/installation/install-linux/)

操作系统：Centos7

```
# 安装yum源
rpm -ivh https://osquery-packages.s3.amazonaws.com/centos7/noarch/osquery-s3-centos7-repo-1-0.0.noarch.rpm

# 安装
yum install osquery.x86_64 -y

# 配置
cp /usr/share/osquery/osquery.example.conf /etc/osquery/osquery.conf

# 启动
systemctl start osqueryd

# 执行
osqueryi
```

## 使用
### 配置文件
[配置文件官方文档](https://osquery.readthedocs.io/en/stable/deployment/configuration/)

```
vim /etc/osquery/osquery.conf

# 常见配置项如下
```

* options：包含osquery daemon的一些配置

* schedule：定时执行的任务

* decorators：记录result的时候，添加额外的信息到decorations中

* packs：等价于一系列schedule的集合

### Osquery表说明
[schema官方文档](https://osquery.io/schema/3.3.0)

在osqueryi命令行下输入.table查看所有表。

* 返回占用内存最大的10个进程

```
select pid, name, uid, resident_size from processes order by resident_size desc limit 10;
```

* 返回最活跃的10个进程

```
select count(pid) as total, name from processes group by name order by total desc limit 10;
```

* 查看网络端口的进程

```
SELECT DISTINCT process.name, listening.port, listening.address, process.pid FROM processes AS process JOIN listening_ports AS listening ON process.pid = listening.pid;
```

* 查看加载的内核模块

```
select name from kernel_modules;
```


### 基础使用
配置文件核心内容：

```
{
    "options": {
        "config_plugin": "filesystem",
        "logger_plugin": "filesystem",
        "logger_path": "/var/log/osquery"
    },
    "schedule": {
        "cpu_time": {
            "query": "SELECT * FROM cpu_time;",
            "interval": 60
        }
    }
}
```

查看日志文件：

```
tail -f /var/log/osquery/osqueryd.results.log
```

![osquery-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/osquery-1.png)

## 参考资料
[企业安全建设之主机资产管理与分析](http://www.freebuf.com/articles/security-management/127851.html)

[Facebook开源的基于SQL的操作系统检测和监控框架:osquery daemon详解](https://www.cnblogs.com/xuxinkun/p/5640047.html)

[Windows 常见配置文件编写](https://github.com/teoseller/osquery-attck)
