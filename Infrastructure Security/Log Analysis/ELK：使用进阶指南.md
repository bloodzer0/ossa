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
command = 'curl -XDELETE "http://hostname/index-%s"' % week_ago
os.system(command)
```

## Logstash
### Logstash常用命令
Command						| Note
---								| ---
--config.reload.automatic	| 自动重载被修改的配置文件
-f								| 加载配置文件
-t								| 测试配置文件

### Logstash配置详解
* **解析IP**

```
filter {
    geoip {
        source => "代表IP的字段名称"
    }
}
```

![elk-7](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Log%20Analysis/img/elk-7.png)

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

![elk-13](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Log%20Analysis/img/elk-13.png)

### Logstash Patterns
[官方文档](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html)

常用Patterns如下表：

Field				| Note
---					| ---
USERNAME或USER	| 数字、大小写字母、特殊字符(.-_)组成
EMAILLOCALPART	| 邮箱用户名部分

GrokDebugg：[在线地址](http://grokdebug.herokuapp.com/) 需要翻墙使用，否在js会加载不成功，为了解决这个问题，我们可以在本地搭建一个。

* **本地安装grokdebug**：[github地址](https://github.com/nickethier/grokdebug) 

操作系统：Centos7

```
# 安装依赖
yum install openssl-devel.x86_64 gcc.x86_64 -y

# 下载ruby
wget https://ruby.taobao.org/mirrors/ruby/2.1/ruby-2.1.7.tar.gz

# 安装ruby
tar -zxvf ruby-2.1.7.tar.gz && cd ruby-2.1.7/
./configure --prefix=/usr/local/ruby2.1.7
make && make install

# 配置环境变量
echo 'export PATH=/usr/local/ruby2.1.7/bin:$PATH'>>/etc/profile
source /etc/profile

# 安装rubygem
wget http://rubygems.global.ssl.fastly.net/rubygems/rubygems-2.6.2.tgz
tar -zxvf rubygems-2.6.2.tgz && cd rubygems-2.6.2/
ruby setup.rb

# 替换gem源
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
gem sources -l

# 创建安装目录与下载grokdebug
mkdir /usr/local/grokdebug && cd /usr/local/grokdebug
git clone https://github.com/nickethier/grokdebug.git
mv grokdebug/* .
rm -rf grokdebug/

# 查看缺少的组件
ruby config.ru

# 安装组件
gem install bundler
gem install cabin -v=0.5.0
gem install haml -v=3.1.7
gem install jls-grok -v=0.10.10
gem install json -v=1.7.5
gem install kgio -v=2.8.0
gem install rack -v=1.4.1
gem install rack-protection -v=1.2.0
gem install raindrops -v=0.11.0  
gem install shotgun -v=0.9
gem install tilt -v=1.3.3
gem install sinatra -v=1.3.3
gem install unicorn -v=4.6.3
```

* **启动与使用grokdebug**

```
# 启动
nohup bundle exec unicorn -p 8081 -c ./unicorn &
```

![elk-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Log%20Analysis/img/elk-4.png)


## Kibana
### Kibana登录鉴权
* **利用nginx代理进行鉴权**

```
# 安装nginx与nginx认证模块
yum install epel-release -y
yum install nginx.x86_64 httpd-tools.x86_64 -y

# 修改nginx配置文件
vim /etc/nginx/nginx.conf
# 注释掉server内的内容，并添加如下内容
```

![elk-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Log%20Analysis/img/elk-5.png)

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

![elk-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Log%20Analysis/img/elk-6.png)

### 使用xpack进行认证
[xpack破解地址](https://www.cnblogs.com/chengjiawei/p/8991859.html)
