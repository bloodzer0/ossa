# ELK高级使用

## ElasticSearch
### ES插件
* **Head**

[官网地址](http://mobz.github.io/elasticsearch-head/)

[Chrome插件：ElasticSearch Head](https://chrome.google.com/webstore/search/elasticsearch)

* **ElasticHD**

### ElasticSearch创建template
```
curl -XPUT 'http://host/_template/primeledger' \ 
    -H "Content-Type: application/json" \ 
    -d '{"order": 0,"template": "template_name*","settings": \ 
    {"index.number_of_shards": 3,"number_of_replicas": 1}}'
```

### ElasticSearch定期清理索引
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Time    : 2018/6/8 上午11:20
# Author  : bloodzer0
# File    : delete_index.py

import os
import datetime

week_ago = (datetime.datetime.now() - datetime.timedelta(days=3)).strftime("%Y.%m.%d")
command = 'curl -XDELETE "http://hostname/index-%s"' % week_ago
os.system(command)
```

## Logstash
### 常用命令
Command						| Note
---								| ---
--config.reload.automatic	| 自动重载被修改的配置文件
-f								| 加载配置文件
-t								| 测试配置文件

### 配置文件
* **解析IP**

```
filter {
    geoip {
        source => "代表IP的字段名称"
    }
}
```

* **多行合并**

```
input {
    file {
        path => ["log_path"]
        codec => multiline {
            pattern => "^%{TIMESTAMP_ISO8601}" # 不以标准时间格式开头的行合并为一行
            negate => true
            what => "previous"
        }
    }
}
```

## Kibana
### Kibana插件
* **汉化**

[github地址](https://github.com/anbai-inc/Kibana_Hanization/)

### Kibana登录鉴权
* **利用nginx代理进行访问认证**

```
# 安装nginx与nginx认证模块
yum install epel-release -y
yum install nginx.x86_64 httpd-tools.x86_64 -y

# 修改nginx配置文件
vim /etc/nginx/nginx.conf
# 注释掉server内的内容，并添加如下内容
```

```
server {
    listen 8080;
    server_name kibana;
    auth_basic "Restricted Access";
    auth_basic_user_file /etc/nginx/kibana-user;

    location / {
        proxy_pass http://hostname:5601;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

配置账号密码

```
# 设置账号密码
htpasswd -cm /etc/nginx/kibana-user username

# 重启nginx服务
systemctl restart nginx.service
```

* **利用xpack进行访问认证**

[xpack破解地址](https://www.cnblogs.com/chengjiawei/p/8991859.html)

## 参考资料
[Elasticsearch史上最全最常用工具清单](https://mp.weixin.qq.com/s/s2ema4tIXKcqTNUUhjGt1w)
