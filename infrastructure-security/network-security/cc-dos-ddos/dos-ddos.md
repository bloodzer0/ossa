# 拒绝服务攻击
* **DoS（Denial of Service）：拒绝服务**

通过对主机特定漏洞的利用攻击导致网络栈失效、系统奔溃、主机死机等而无法提供正常的网络服务功能，从而造成拒绝服务。

* **DDoS（Distributed Denial of Service）：分布式拒绝服务**

通过很多"僵尸主机"向受害主机发送大量看似正常的网络包，从而造成网络阻塞或服务器资源耗尽而导致拒绝服务。

## 拒绝服务攻击分类
### 网络层攻击
* **UDP-Flood**

由于UDP协议是一种无连接的服务，在UDPFLOOD攻击中，攻击者可发送大量伪造源IP地址的小UDP包。但是，由于UDP协议是无连接性的，所以只要开了一个UDP的端口提供相关服务的话，那么就可针对相关的服务进行攻击。-- 百度百科

防御技巧：

```
摘自"百度百科"
攻击业务端口：根据该业务UDP最大包长设置UDP最大包大小以过滤异常流量。 

攻击非业务端口：
	一个是丢弃所有UDP包，可能会误伤正常业务。
	一个是建立UDP连接规则，要求所有去往该端口的UDP包，必须首先与TCP端口建立TCP连接，需要防火墙支撑。
```

* **TCP-Flood**

TCP攻击俗称SYN泛洪，利用TCP三次握手协议来攻击计算机，主要是通过伪造大量IP的半连接去攻击对方系统。

* **ICMP-Flood**

死亡之ping，正常ICMP协议数据包为96字节，攻击者使用的ICMPFlood通常设定较大的长度。

### 协议层攻击
* **TCP/UDP/ICMP分片**

当协议栈收到分片通知时，需要预留一定的内存来组装数据包，攻击者恶意使用分片功能时，就会导致服务端的协议栈资源消耗过大。

* **SYN-Flood**

正常流程：客户端发送SYC，服务端响应SYN+ACK，客户端回传ACK+1。

攻击流程：伪造不存在的IP发送请求，服务端接受不到ACK+1，会持续发送SYN+ACK，同时保持着未完成的连接，这样容易占满服务器的连接队列达到效果。

* **ACK-Flood**

与SYN-Flood攻击类似，攻击者发送ACK包，如果目标系统允许ACK来建立连接就很容易导致连接表项资源耗尽，同时协议栈还会发现这类数据包不合法和响应reset包，导致系统资源消耗过大。

* **zeroWindow**

建立连接后，发送数据请求，服务端准备好数据时，攻击者发送ack包指定window大小为0，这是服务端无法发送数据处于等待状态，通过建立大量这样的连接来消耗服务端资源。

* **Connection-Flood**

仅建立连接不发送数据，由于服务器等待超时，在这个过程中建立大量这样的连接，实现拒绝服务攻击。

* **SSL-Flood**

不断的发起SSL握手，SSL握手过程，服务端消耗的资源是客户端的15倍。

* **SSLKeyRenego**

在协商加密算法的时候服务端CPU的消耗是客户端的15倍左右，而Renegotiating机制可以让攻击者在一个TCP连接中不断进行重新协商，导致服务端CPU资源耗尽。

防御技巧：

```
禁用Renegotiating机制来实现防御
```

### 反射放大攻击
反射：无论DNS还是NTP，都是基于UDP协议的，在UDP协议中正常客户端发送请求包到服务端，服务端返回响应包到客户端，但是UDP协议是面向无连接的，所以客户端发送请求包的源IP很容易进行伪造，当把源IP修改为受害者的IP，最终服务端返回的响应包就会返回到受害者的IP，这样就形成了反射攻击。

放大：一个小的请求包最终会受到一个或者多个多于请求包许多倍的响应包，这样就达到了四两拨千斤的效果。

* **DNS反射**

利用互联网上开放的DNS地柜服务器作为攻击源，利用"反弹"手法攻击目标机器。

正常流程：源IP地址--DNS查询--DNS服务器--DNS回复包--源IP地址

攻击流程：伪造IP地址(受害者IP)--DNS查询--DNS服务器--DNS回复包--伪造的IP地址(攻击目标)

* **NTP反射**

NTP包含一个monlist功能，也被称为MON_GETLIST，主要用于监控NTP服务器，NTP服务器响应monlist后悔返回与NTP服务器进行过时间同步的最后600个客户端IP，响应包按照每6个IP进行分割，最多有100个响应包。

* **SNMP反射**

* **SSDP反射**

* **Chargen反射**

### 其它类型
* **DRDoS**

DRDoS（Distributed Reflection Denial of Service）：分布式反射攻击

* **ReDoS**

ReDoS（Regular Expression Denial of Service）：正则表达式拒绝服务攻击，开发人员使用了正则表达式来对用户输入的数据进行有效性校验，当编写校验的正则表达式存在缺陷或者不严谨时，攻击者可以构造特殊的字符串来大量消耗服务器的系统资源，造成服务器的服务终端或停止。

* **无线DDoS**

## 测试工具
### hping3
[官网地址](http://www.hping.org/hping3.html)

* **Centos7编译安装，kali自带**

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

![ddos-1](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/ddos-1.png)

```
# 修改文件，修改内容如图
vim bytesex.h
```

![ddos-2](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/ddos-2.png)

```
# 编译完成，进行安装
make

# 报错如图
```

![ddos-3](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/ddos-3.png)

```
# 链接文件
ln -s /usr/include/pcap-bpf.h /usr/include/net/bpf.h

# 再次安装
make && make install
```

* **hping3使用案例**

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
* **Centos7编译安装，kali自带**

```
# 解决环境依赖
yum install gcc.x86_64 openssl-devel.x86_64 -y

# 下载
git clone git://git.kali.org/packages/thc-ssl-dos.git && cd thc-ssl-dos

# 编译安装
./configure
make && make install
```

* **Thc-SSL-DoS使用案例**

```
thc-ssl-dos -l 500 IP PORT --accept
```

## 拒绝服务攻击防御
本质：网络层面的DDoS攻击是无法防御的，能做的就是不断优化自身的网络架构，以及提升网络带宽。

### 增加链路带宽

### ISP/WAN层
电信云堤产品提供"流量压制"和"近源清洗"：

* **流量压制**

分方向的黑洞路由

* **近源清洗**

### CDN/Internet层
CDN本身不是抗DDoS产品，但是针对Web服务而言，CDN可以提供一定的抗D效果。

### DC层
DataCenter这一层的防御属于近目的清洗，在DC出口的地方部署ADS设备。

### OS/APP层
应用层防御。

## 参考资料
[五步七招，开启最强DDoS攻防战！](https://dbaplus.cn/news-21-1005-1.html)

[DDoS攻击方式总结](https://www.secpulse.com/archives/64088.html)

[基于Memcached分布式系统DRDoS拒绝服务攻击技术研究](https://blog.csdn.net/microzone/article/details/79262549)

[浅析ReDoS的原理与实践](https://www.freebuf.com/articles/network/124422.html)

[浅谈DDoS攻击与防御](https://thief.one/2017/05/10/1/)

![ddos](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/ddos.png)
