## 概述

## 安装ELK+Bro
[Bro安装指南](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/Bro%EF%BC%9A%E7%BD%91%E7%BB%9C%E5%AE%89%E5%85%A8%E7%9B%91%E6%8E%A7.md)

[ELK安装指南](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/ELK%EF%BC%9A%E5%AE%89%E8%A3%85%E4%B8%8E%E5%9F%BA%E7%A1%80%E4%BD%BF%E7%94%A8.md)

查看Bro中SSH的日志：

```
cat /opt/bro/logs/current/ssh.log
```

![bro-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/bro-4.png)

编辑Logstash配置文件：

```
vim /etc/logstash/conf.d/bro.conf
```

bro.conf内容：

```
input {
    file {
        path => ["/opt/bro/logs/current/ssh.log"]
        type => "bro-ssh"
    }
}
filter {
    if [message] =~ /^#/ {  # 过滤日志文件顶部注释行
        drop {}
    }
    if [type] == "bro-ssh" {
        csv {
            columns => ["ts","uid","id.orig_h","id.orig_p","id.resp_h","id.resp_p","version","auth_success","auth_attempts","direction","client","server","cipher_alg","mac_alg","compression_alg","kex_alg","host_key_alg","host_key","remote_location.country_code","remote_location.region","remote_location.city","remote_location.latitude","remote_location.longitude"] # csv允许使用逗号作为默认分隔符，字段就是日志文件的头定义的字段
            separator => "  "
        }

        date {
            match => ["ts","UNIX"] # 定义时间戳
        }
        geoip {
            source => "id.orig_h" # 解析IP
        }
    }
}

output {
    elasticsearch {
        hosts => ["localhost:9200"]
        index => "logstash-%{+YYYY.MM}"
    }
}
```

启动对应的服务后

![bro-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/bro-5.png)
