# ELK监控报警系统-elastalert
[github地址](https://github.com/Yelp/elastalert/)

## elastalert安装
```
# 操作系统：Centos7
# 解决python依赖
yum install epel-release -y

yum install python2-pip.noarch gcc.x86_64 python-devel.x86_64 libffi-devel.x86_64 git.x86_64 -y

pip install --upgrade pip

pip install "setuptools>=11.3"
pip install "elasticsearch>=5.0.0"

# 安装
git clone https://github.com/Yelp/elastalert.git
cd elastalert/ && pip install -r requirements.txt
python setup.py install
```

## 使用
* **创建索引**

```
elastalert-create-index
```

* **测试规则文件**

```
elastalert-test-rule rule.yaml
```

* **启动监控报警**

```
python -m elastalert.elastalert --verbose --rule /root/elastalert/example_rules/rule.yaml
```

### 以服务启动elastalert
```
mkdir /etc/elastalert
cp /root/elastalert/config.yaml.example /etc/elastalert/config.yaml

# 创建规则目录
mkdir /etc/elastalert/rules
cp /root/elastalert/example_rules/example_frequency.yaml /etc/elastalert/rules/
cp /root/elastalert/example_rules/example_frequency.yaml /etc/elastalert/rules/rule.yaml

# 修改配置文件
vim /etc/elastalert/config.yaml
	rules_folder: /etc/elastalert/rules

# 创建elastalert服务文件
vim /etc/systemd/system/elastalert.service
```

elastalert.service文件内容：

```
[Unit]
Description=elastalert
After=elasticsearch.service
	
[Service]
Type=simple
User=root
Group=root
Restart=on-failure
WorkingDirectory=/opt
ExecStart=/usr/bin/python -m elastalert.elastalert --config /etc/elastalert/config.yaml --rule /etc/elastalert/rules/rule.yaml
	
Install]
WantedBy=multi-user.target
```

启动服务：

```
systemctl start elastalert.service
```

### config.yaml配置文件说明
```
rules_folder: /etc/elastalert/rules # 规则目录

run_every: # 多久从ES中查询一次
	minutes: 1
	
es_host: 172.16.64.171
es_port: 9200

writeback_index: elastalert_status # elastalert索引名称

alert_time_limit: # 失败重试限制
	days: 2
```

### 邮箱报警

```
vim /etc/elastalert/rules/rule.yaml
```

rule.yaml文件内容：

```
es_host: 127.0.0.1
es_port: 9200

# 规则名字，唯一值
name: security test

# 报警类型，参考印象笔记
type: any

# es索引
index: logstash-*

# 过滤
filter:
- query:
        query_string:
                query: "message:test"

# 报警类型
alert:
- "email"
smtp_host: smtp.126.com
smtp_port: 25
smtp_auth_file: smtp_auth_file.yaml
email_reply_to: email1_address
from_addr: email1_address

# 接受邮箱
email:
- "email2_address"
```

创建邮箱密码文件:

```
vim /etc/elastalert/rules/smtp_auth_file.yaml
```

smtp_auth_file.yaml文件内容：

```
user: "username"
password: "password"
```

### 钉钉报警
[github地址](https://github.com/xuyaoqiang/elastalert-dingtalk-plugin)

* **钉钉测试**

```
curl 'https://oapi.dingtalk.com/robot/send?access_token=token值' \ 
    -d '{"msgtype":"text","text":{"content":"test"}}' \ 
    -H 'Content-Type:application/json'
```

* **安装插件**

```
# 下载
git clone https://github.com/xuyaoqiang/elastalert-dingtalk-plugin.git

# 复制插件到elastalert中
cp -r elastalert-dingtalk-plugin/elastalert_modules/ /etc/elastalert/
```

* **配置规则**

```
vim /etc/elastalert/rules/rule.yaml
```

rule.yaml文件内容：

```
es_host: 127.0.0.1
es_port: 9200
name: security
type: any
index: logstash

# 查询模式
filter:
- query:
        query_string:
                query: "message:test"

# 报警模式
alert:
- "elastalert_modules.dingtalk_alert.DingTalkAlerter"

# 钉钉的webhook接口
dingtalk_webhook: ""
dingtalk_msgtype: "text"
```

启动监控报警:

```
python -m elastalert.elastalert --verbose --config /etc/elastalert/config.yaml --rule /etc/elastalert/rules/rule.yaml
```

* **配置服务启动**

```
vim /etc/systemd/system/elastalert.service
```

elastalert.service文件内容：

```
[Unit]
Description=elastalert
After=elasticsearch.service
	
[Service]
Type=simple
User=root
Group=root
Restart=on-failure
WorkingDirectory=/opt
ExecStart=cd /etc/elastalert && /usr/bin/python -m elastalert.elastalert --config /etc/elastalert/config.yaml --rule /etc/elastalert/rules/rule.yaml
	
Install]
WantedBy=multi-user.target
```

* **优化报警格式**

```
# 注意上下字段对应
alert_text: |
    kibana_url: "https://hostname:5601/app/kibana"
    alarm_reason: "1分钟内login.php至少被访问10次"
    alarm_name: {}
    request_uri: {}
    request_ip: {}
    response_status: {}
alert_text_args:
    - name
    - request
    - clientip
    - response
alert_text_type: alert_text_only
```

### elastalert-kibana-plugin
[github地址](https://github.com/bitsensor/elastalert-kibana-plugin)

## 参考资料
[一个人的安全部之ELK接收Paloalto日志并用钉钉告警](https://www.freebuf.com/articles/others-articles/161905.html)

[基于Elastalert的安全告警剖析](https://www.freebuf.com/sectool/164591.html)
