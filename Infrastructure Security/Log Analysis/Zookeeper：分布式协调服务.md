## 概述
[官网地址](http://zookeeper.apache.org/)

## 安装
### Zookeeper安装
```
# 下载并解压到指定目录
tar -xf zookeeper-3.4.13.tar.gz -C /app/

# 配置环境变量
vim /etc/profile
	export ZOOKEEPER_HOME=/app/zookeeper-3.4.13
	export PATH=$ZOOKEEPER_HOME/bin:$PATH

source /etc/profile

# 负责配置文件
cp /app/zookeeper-3.4.13/conf/zoo_sample.cfg /app/zookeeper-3.4.13/conf/zoo.cfg

# 启动Zookeeper
zkServer.sh start
```

![zookeeper-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/zookeeper-1.png)

### Zookeeper配置文件
vim /app/zookeeper-3.4.13/conf/zoo.cfg

```
tickTime=2000 # 
initLimit=10 # 
syncLimit=5 # 
dataDir=/tmp/zookeeper # 
clientPort=2181 # 
```

## 使用
### Zookeeper客户端使用
zkCli.sh

![zookeeper-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Log%20Analysis/img/zookeeper-2.png)

## 踩坑记

## 参考资料
