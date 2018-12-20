# Graylog高级使用

## Graylog解析Nginx日志

数据流向：Filebeat->Collector->Filebeat-Graylog Server

### 创建一个Beats类型的Inputs
system/overview-Inputs

![graylog-3](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-3.png)

### 配置Collector收集日志
system/overview-Collector-Mange Configurations-Create configration

![graylog-4](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-4.png)

修改配置-Create Output-Filebeat Output

![graylog-5](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-5.png)

![graylog-6](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-6.png)

修改配置-Create Input-Filebeat Input，记得修改Nginx日志文件路径

![graylog-7](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-7.png)

![graylog-8](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-8.png)

修改标签

![graylog-9](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-9.png)

### Collector配置
* **安装collector**

```
wget https://github.com/Graylog2/collector-sidecar/releases/download/0.1.7/collector-sidecar-0.1.7-1.x86_64.rpm

rpm -ivh collector-sidecar-0.1.7-1.x86_64.rpm
```

* **配置collector**

```
vim /etc/graylog/collector-sidecar/collector_sidecar.yml
# 修改tag标签就好了
```

![graylog-10](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-10.png)

* **启动collector**

```
graylog-collector-sidecar install
```

### 配置解析IP
* **下载解析文件**

[下载地址](https://dev.maxmind.com/zh-hans/geoip/geoip2/geolite2/)

```
# 解压并存放文件到指定路径
tar -xf GeoLite2-City.tar.gz
mv GeoLite2-City_20181218/GeoLite2-City.mmdb /etc/graylog/server/

tar -xf GeoLite2-Country.tar.gz
mv GeoLite2-Country_20181218/GeoLite2-Country.mmdb /etc/graylog/server/
```

* **修改Inputs的Manage Extractors**

![graylog-11](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-11.png)

Add extractor-Load Message（备注：再次之前，必须保证你的Ningx日志文件中有日志信息）

![graylog-12](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-12.png)

在message的右侧，选择Select extractor tyep为Grok Pattern

![graylog-13](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-13.png)

![graylog-14](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-14.png)

![graylog-15](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-15.png)

* **配置解析库**
system/overview-Configurations-Geo-Location Processor

点击update

![graylog-16](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-16.png)

效果图

![graylog-17](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/graylog-17.png)

## 参考资料
[Graylog分析Nginx日志并通过GeoIP2获取访问者IP的地理位置信息](http://blog.51cto.com/bigboss/2135163)
