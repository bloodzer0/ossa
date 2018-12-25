# 网络安全监控：Bro
[官网地址](https://www.bro.org/)

[学习平台](https://www.bro.org/documentation/index.html)

## 安装与使用
### Centos7安装Bro
* **解决依赖**

```
yum install cmake.x86_64 gcc.x86_64 gcc-c++.x86_64 flex.x86_64 bison.x86_64 libpcap.x86_64 libpcap-devel.x86_64 openssl-devel.x86_64 python-devel.x86_64 swig.x86_64 zlib-devel.x86_64 -y
```

* **yum安装**

```
# 配置yum源
cd /etc/yum.repos.d/
wget http://download.opensuse.org/repositories/network:bro/CentOS_7/network:bro.repo

# 安装
yum install bro.x86_64 -y

# 配置运行环境
vim /etc/profile
```

```
export PATH=/opt/bro/bin:$PATH
```

```
source /etc/profile
```

* **编译安装**

```
# 下载(推荐)
wget https://www.bro.org/downloads/bro-2.5.5.tar.gz

# 或者通过git下载
git clone --recursive git://git.bro.org/bro

# 安装
tar -xf bro-2.5.5.tar.gz && cd bro-2.5.5
./configure
make && make install

# 此时的安装路径为：/usr/local/bro
```

### Bro配置文件
```
# $PREFIX：安装bro所在的目录，视具体环境所定。

$PREFIX/etc/node.cfg 监听网卡文件
$PREFIX/etc/network.cfg 默认设置
$PREFIX/etc/broctl.cfg 设置邮箱MailTo，并且设置LogRotationInterval改变日志压缩频率
```

### Bro基础使用
* **BroControl**

```
# 配置监听网卡，我的本地网卡是ens33，修改如图
vim /usr/local/bro/etc/node.cfg

# 执行broctl
```

![bro-1](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/bro-1.png)

![bro-2](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/bro-2.png)

```
# 查看日志
ls -l /usr/local/bro/logs/current/

# 会话日志：conn.log
# 告警日志：
	weird.log # 协议错误
	notice.log # bro脚本产生的告警
# 协议解析日志：dns.log、files.log、http.log、sip.log、snmp.log
```

![bro-3](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/bro-3.png)

* **Bro Command-Line**

```
bro -i ens33 # 选择监听接口，此时日志在执行目录中
bro -r *.pcap # 读取一个pacp进行分析
bro -C # 禁用校验和
bro -f # 捕获流量时进行过滤
```

* **bro_cut**

类似awk，提取指定的列

### Bro Scripts

## Bro+ELK实现入侵检测日志分析
[ELK安装文档](https://bloodzer0.github.io/ossa/infrastructure-security/host-security/log-analysis/elk1/)

### 分析SSH登录日志
查看Bro中SSH的日志：

```
cat /usr/local/bro/logs/current/ssh.log
```

![bro-4](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/bro-4.png)

编辑logstash配置文件：

```
wget https://raw.githubusercontent.com/fakrul/bro-elk/master/bro-ssh_log.conf

# 修改日志文件路径
# 修改es地址与索引名称
# 查看效果
```

![bro-5](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/bro-5.png)

## 参考资料
[使用Bro IDS和Intel Critical Stack分析网络活动](https://bbs.pediy.com/thread-226565.htm)
