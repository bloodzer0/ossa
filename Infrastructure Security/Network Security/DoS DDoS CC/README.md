DoS（Denial of Service）：拒绝服务

```
通过对主机特定漏洞的利用攻击导致网络栈失效、系统奔溃、主机死机等而无法提供正常的网络服务功能，从而造成拒绝服务。
```

DDoS（Distributed Denial of Service）：分布式拒绝服务

```
通过很多"僵尸主机"向受害主机发送大量看似正常的网络包，从而造成网络阻塞或服务器资源耗尽而导致拒绝服务。
```

CC（Chanllenge Collapsar）：CC就是DDoS的一种

```
通过控制某些主机补丁发送大量数据包给服务器造成服务器资源耗尽，一直到宕机崩溃，常用于攻击页面。
```

## 根据攻击类型分类
### 网络层攻击
* UDP-Flood

由于UDP协议是一种无连接的服务，在UDPFLOOD攻击中，攻击者可发送大量伪造源IP地址的小UDP包。但是，由于UDP协议是无连接性的，所以只要开了一个UDP的端口提供相关服务的话，那么就可针对相关的服务进行攻击。-- 百度百科

防御技巧:

```
摘自"百度百科"
攻击业务端口：根据该业务UDP最大包长设置UDP最大包大小以过滤异常流量。 

攻击非业务端口：
	一个是丢弃所有UDP包，可能会误伤正常业务。
	一个是建立UDP连接规则，要求所有去往该端口的UDP包，必须首先与TCP端口建立TCP连接，需要防火墙支撑。
```

[测试工具：LOIC](https://sourceforge.net/projects/loic/)

* TCP-Flood

TCP攻击俗称SYN泛洪，利用TCP三次握手协议来攻击计算机，主要是通过伪造大量IP的半连接去攻击对方系统。

[测试工具：hping3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Network%20Security/DoS%20DDoS%20CC/DDoS.md)

* ICMP-Flood

死亡之ping，正常ICMP协议数据包为96字节，攻击者使用的ICMPFlood通常设定较大的长度。


### 协议层攻击
* TCP/UPD/ICMP分片

当协议栈收到分片通知时，需要预留一定的内存来组装数据包，攻击者恶意使用分片功能时，就会导致服务端的协议栈资源消耗过大。

* SYN-Flood

正常流程：客户端发送SYC，服务端响应SYN+ACK，客户端回传ACK+1。

攻击流程：伪造不存在的IP发送请求，服务端接受不到ACK+1，会持续发送SYN+ACK，同时保持着未完成的连接，这样容易占满服务器的连接队列达到效果。

* ACK-Flood

与SYN-Flood攻击类似，攻击者发送ACK包，如果目标系统允许ACK来建立连接就很容易导致连接表项资源耗尽，同时协议栈还会发现这类数据包不合法和响应reset包，导致系统资源消耗过大。

* zeroWindow

建立连接后，发送数据请求，服务端准备好数据时，攻击者发送ack包指定window大小为0，这是服务端无法发送数据处于等待状态，通过建立大量这样的连接来消耗服务端资源。

* Connection-Flood

仅建立连接不发送数据，由于服务器等待超时，在这个过程中建立大量这样的连接，实现拒绝服务攻击。

* SSL-Flood

不断的发起SSL握手，SSL握手过程，服务端消耗的资源是客户端的15倍。

* SSLKeyRenego

在协商加密算法的时候服务端CPU的消耗是客户端的15倍左右，而Renegotiating机制可以让攻击者在一个TCP连接中不断进行重新协商，导致服务端CPU资源耗尽。

参考资料：[超强DDoS工具SSL DoS攻击测试详解](http://www.jybase.net/wangzhananquan/20120320804_2.html)

[测试工具：thc-ssl-dos](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Network%20Security/DoS%20DDoS%20CC/DDoS.md)

防御技巧：

```
禁用Renegotiating机制来实现防御。
```

### 应用层攻击
[测试工具参考](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Network%20Security/DoS%20DDoS%20CC/CC.md)

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
反射：无论DNS还是NTP，都是基于UDP协议的，在UDP协议中正常客户端发送请求包到服务端，服务端返回响应包到客户端，但是UDP协议是面向无连接的，所以客户端发送请求包的源IP很容易进行伪造，当把源IP修改为受害者的IP，最终服务端返回的响应包就会返回到受害者的IP，这样就形成了反射攻击。

放大：一个小的请求包最终会受到一个或者多个多于请求包许多倍的响应包，这样就达到了四两拨千斤的效果。

* DNS反射

利用互联网上开放的DNS地柜服务器作为攻击源，利用"反弹"手法攻击目标机器。

正常流程：源IP地址--DNS查询--DNS服务器--DNS回复包--源IP地址

攻击流程：伪造IP地址(受害者IP)--DNS查询--DNS服务器--DNS回复包--伪造的IP地址(攻击目标)

* NTP反射

NTP包含一个monlist功能，也被称为MON_GETLIST，主要用于监控NTP服务器，NTP服务器响应monlist后悔返回与NTP服务器进行过时间同步的最后600个客户端IP，响应包按照每6个IP进行分割，最多有100个响应包。

* SNMP反射

* SSDP反射

* Chargen反射

## 参考资料
[DDoS攻击方式总结](https://www.secpulse.com/archives/64088.html)

来自网易-林伟壕的DDoS分类图：

![DDoS攻击与防御](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Network%20Security/DoS%20DDoS%20CC/img/DDoS%E6%94%BB%E5%87%BB%E4%B8%8E%E9%98%B2%E5%BE%A1.png)
