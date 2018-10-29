DoS（Denial of Service）：拒绝服务

```
通过对主机特定漏洞的利用攻击导致网络栈失效、系统奔溃、主机死机等而无法提供正常的网络服务功能，从而造成拒绝服务。
```

DDoS（Distributed Denial of Service）：分布式拒绝服务

```
通过很多"僵尸主机"向受害主机发送大量看似正常的网络包，从而造成网络阻塞或服务器资源耗尽而导致拒绝服务。
```

CC（Chanllenge Collapsar）

```
通过控制某些主机补丁发送大量数据包给服务器造成服务器资源耗尽，一直到宕机崩溃，常用于攻击页面。
```

## 根据攻击类型分类
### 网络层攻击
* UDP-Flood

* TCP-Flood

* ICMP-Flood

死亡之ping，正常ICMP协议数据包为96字节，攻击者使用的ICMPFlood通常设定较大的长度。


### 协议层攻击
* TCP/UPD/ICMP分片

* SYN-Flood

* ACK-Flood

* zeroWindow

* Connection-Flood

* SSL-Flood

* SSLKeyRenego


### 应用层攻击
* Get-Flood

直接对较为消耗资源的URL进行大量的请求，也可以通过多个URL并发，单一URL并发请求，随机URL请求等。

* SlowLoris

也称为slow headers，攻击者始终构造不完整的http header发送，同时还间隔一定时间再发送数据，导致服务器一直处于等待状态。

* SlowPost

也称为slow body，攻击的重点在POST数据上，通过将HTTP Header中的Content-Length设置为一个较大的值，同时还间隔一定时间再发送数据，导致服务器一直处于等待状态中。

* SlowRead

把window窗口设置为一个非常小的值，让服务器始终准备回应数据。

* HashDoS


### 反射放大攻击
* DNS反射

* NTP反射

* SNMP反射

* SSDP反射

* Chargen反射
