## 概述

## 安装
操作系统：Centos7

### 解决依赖
```
yum install cmake.x86_64 gcc.x86_64 gcc-c++.x86_64 flex.x86_64 bison.x86_64 libpcap.x86_64 libpcap-devel.x86_64 openssl-devel.x86_64 python-devel.x86_64 swig.x86_64 zlib-devel.x86_64 -y
```

### 安装Bro
* yum安装

```
# 配置yum源
cd /etc/yum.repos.d/ && wget http://download.opensuse.org/repositories/network:bro/CentOS_7/network:bro.repo

# 安装
yum install bro.x86_64 -y

# 配置运行环境
vim /etc/profile
	export PATH=/opt/bro/bin:$PATH
source /etc/profile
```

* 编译安装

```
# 下载(推荐)
wget https://www.bro.org/downloads/bro-2.5.5.tar.gz

# git下载
git clone --recursive git://git.bro.org/bro

# 安装
tar -xf bro-2.5.5.tar.gz && cd bro-2.5.5
./configure
make && make install

# 此时的安装路径为：/usr/local/bro
```

## 配置与使用Bro
### 配置Bro
* 配置文件

```
# $PREFIX：安装bro所在的目录，视具体环境所定。

$PREFIX/etc/node.cfg 监听网卡文件
$PREFIX/etc/network.cfg 默认设置
$PREFIX/etc/broctl.cfg 设置邮箱MailTo，并且设置LogRotationInterval改变日志压缩频率
```

* 简单测试：使用BroControl

```
# 配置监听网卡，我的本地网卡是ens33，修改如图
vim /opt/bro/etc/node.cfg
```

![bro-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/bro-1.png)

![bro-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/bro-2.png)

```
# 查看日志，每个人可能看到的不一样
ls -l /opt/bro/logs/current/
```

![bro-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/IDS%20IPS/img/bro-3.png)

* 简单测试：使用Bro Command-Line

```
# 指定接口，此时日志在执行目录中
bro -i ens33
```

### 使用Bro
* bro_cut：类似awk，提取指定的列
