## 概述
### http
HyperText Transfer Protocol，超文本传输协议，是互联网上使用最广泛的一种协议，所有www文件必须遵循的标准。http协议传输的数据都是未加密的，也就是明文的，因此使用http协议传输隐私信息非常不安全。

使用TCP端口为：80

### https
HyperText Transfer Protocol Over Secure Socket Layer，安全的超文本传输协议。

### SSL协议加密方式
SSL协议即用到了对称加密也用到了非对称加密(公钥加密)，在建立传输链路时，SSL首先对对称加密的密钥使用公钥进行非对称加密，链路建立好之后，SSL对传输内容实用对称加密。

* 对称加密：速度高，可加密内容较大，用来加密会话过程中的消息。
* 公钥加密：加密速度较慢，但能提供更好的身份认证技术，用来加密对称加密的密钥。

## https单向认证
![https-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Transport%20Security/img/https-1.png)

## https双向认证
![https-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Transport%20Security/img/https-2.png)
