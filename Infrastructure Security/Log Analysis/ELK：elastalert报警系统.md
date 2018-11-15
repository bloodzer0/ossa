## 概述
[github地址](https://github.com/Yelp/elastalert/)

## 安装
操作系统：Centos7（默认已经安装完成ELK环境）

```
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
### 基本使用
```
# 创建elastalert索引
elastalert-create-index
```

![elk-8](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-8.png)

```
# 测试规则文件是否正确
elastalert-test-rule rule.yaml

# 启用报警
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
	
# 启动服务
systemctl start elastalert.service
```

* config.yaml说明

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
编写规则文件：vim /etc/elastalert/rules/rule.yaml

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

编辑smtp_auth_file.yaml：vim /etc/elastalert/rules/smtp_auth_file.yaml

```
user: "username"
password: "password"
```

临时启动：

```
python -m elastalert.elastalert --verbose --rule /etc/elastalert/rules/rule.yaml --config /etc/elastalert/config.yaml
```

![elk-9](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-9.png)

查看报警：

![elk-10](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-10.png)

### 钉钉报警
[github地址](https://github.com/xuyaoqiang/elastalert-dingtalk-plugin)

* 测试钉钉

```
curl 'https://oapi.dingtalk.com/robot/send?access_token=token值' -d '{"msgtype":"text","text":{"content":"test"}}' -H 'Content-Type:application/json'
```

* 安装插件

```
# 下载
git clone https://github.com/xuyaoqiang/elastalert-dingtalk-plugin.git

# 复制插件到elastalert中
cp -r elastalert-dingtalk-plugin/elastalert_modules/ /etc/elastalert/
```

* 编写rule文件：vim /etc/elastalert/rules/rule.yaml

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

* 执行测试（此时必须用python命令行的模式执行，并且执行目录在/etc/elastalert/，因为需要导入模块）

```
python -m elastalert.elastalert --verbose --config /etc/elastalert/config.yaml --rule /etc/elastalert/rules/rule.yaml
```

![elk-11](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-11.png)

效果如图：

![elk-12](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/elk-12.png)


* 以服务启动方式解决

在配置好elastalert_modules目录为/etc/elastalert中是，修改服务启动文件：vim /etc/systemd/system/elastalert.service

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

## 报警优化
### 报警格式优化
注意上下字段对应

```
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

## elasialert-kibana-plugin
[github地址](https://github.com/bitsensor/elastalert-kibana-plugin)

## 参考资料
[一个人的安全部之ELK接收Paloalto日志并用钉钉告警](https://www.freebuf.com/articles/others-articles/161905.html)

[基于Elastalert的安全告警剖析](https://www.freebuf.com/sectool/164591.html)
