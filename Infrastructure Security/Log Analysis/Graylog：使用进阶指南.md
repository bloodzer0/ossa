## 概述

## Nginx日志解析
数据流向

Filebeat--Collector--Filebeat--Graylog Server

### 配置Graylog接受Nginx日志
* 创建一个Beats类型的Inputs

![graylog-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-4.png)

* 配置Collector来收集日志

![graylog-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-5.png)

* 修改配置

![graylog-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-6.png)

![graylog-7](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-7.png)

* 配置Beats输入源

![graylog-8](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-8.png)

![graylog-9](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-9.png)

* 配置标签

![graylog-10](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-10.png)

### 安装collector
```
wget https://github.com/Graylog2/collector-sidecar/releases/download/0.1.7/collector-sidecar-0.1.7-1.x86_64.rpm

rpm -ivh collector-sidecar-0.1.7-1.x86_64.rpm
```

* 修改Collector配置文件

vim /etc/graylog/collector-sidecar/collector_sidecar.yml

![graylog-11](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-11.png)

* 启动collector

```
graylog-collector-sidecar install
```

![graylog-12](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-12.png)

### 解析IP
下载IP解析文件：[下载地址](https://dev.maxmind.com/zh-hans/geoip/geoip2/geolite2/)

修改Inputs的 Manage Extractors

![graylog-13](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-13.png)

![graylog-14](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-14.png)

首先Load Messages，然后选择messages右侧的Select extractor tyep为Grok pattern

![graylog-15](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-15.png)

配置IP解析库插件：System-->Configurations

![graylog-16](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-16.png)

![graylog-17](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-17.png)

成功图：

![graylog-18](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/graylog-18.png)

## 参考资料
[Graylog分析Nginx日志并通过GeoIP2获取访问者IP的地理位置信息](http://blog.51cto.com/bigboss/2135163)
