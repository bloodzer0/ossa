## 概述

## 利用工具
### hping3
[Hping3官网地址](http://www.hping.org/hping3.html)

* Kali自带

* Centos7编译安装

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

## 防御
