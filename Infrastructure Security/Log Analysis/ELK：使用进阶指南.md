## ElasticSearch
### ElasticSearch创建template
```
curl -XPUT 'http://host/_template/primeledger' -H "Content-Type: application/json" -d '{"order": 0,"template": "template_name*","settings": {"index.number_of_shards": 3,"number_of_replicas": 1}}'
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
command = 'curl -XDELETE "http://host/index-%s"' % week_ago
os.system(command)
```

### ElasticSearch搜索总结
* 查询需要的字段

```

```

* 查询区间

```

```

* 展示指定的字段

```

```

* 正则查询

```

```

## Logstash
### Logstash配置详解
* 解析IP

```
filter {
    geoip {
        source => "代表IP的字段名称"
    }
}
```


## Kibana
### Kibana登录鉴权
* 利用nginx代理进行鉴权

```
# 安装nginx与nginx认证模块
yum install epel-release -y
yum install nginx.x86_64 httpd-tools.x86_64 -y

# 修改nginx配置文件
vim /etc/nginx/nginx.conf
# 注释掉server内的内容，并添加如下内容
```

![elk-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-5.png)

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

设置账号密码

```
# 设置账号密码
htpasswd -cm /etc/nginx/kibana-user username

# 重启nginx服务
systemctl restart nginx.service
```

![elk-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-6.png)
