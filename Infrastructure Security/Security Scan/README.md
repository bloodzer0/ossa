针对主机与网络的扫描主要分为以下的类型：

* ACL扫描：[端口扫描](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Security%20Scan/%E7%AB%AF%E5%8F%A3%E6%89%AB%E6%8F%8F.md)
* 弱口令扫描：[服务弱口令扫描](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Security%20Scan/%E5%BC%B1%E5%8F%A3%E4%BB%A4%E6%89%AB%E6%8F%8F.md)
* 系统及应用服务漏洞扫描：主要分为应用本身漏洞、应用配置错误漏洞

备注：弱口令扫描同样属于应用配置错误漏洞的一种。

## 扫描的进阶之路
* 不同类型进行分开扫描
* 合并扫描：ACL扫描-->服务探测-->弱口令扫描/应用漏洞扫描/系统漏洞扫描
* 漏扫、误报、处理
