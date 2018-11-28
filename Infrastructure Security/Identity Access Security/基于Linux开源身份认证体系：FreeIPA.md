## 概述
[官网地址](https://www.freeipa.org/)

## 安装与使用
### Centos7安装FreeIPA
[官方安装文档](https://access.redhat.com/products/identity-management#getstarted)

```
# 配置一个DNS解析记录
echo 192.168.199.5 ipa.bloodzer0.com ipa >> /etc/hosts
echo ipa.bloodzer0.com > /etc/hostname

# 安装ipa-server
yum install ipa-server.x86_64 ipa-server-dns.noarch -y

# 开始配置，看图说话
ipa-server-install
```

![freeipa-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/freeipa-1.png)

![freeipa-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/freeipa-2.png)

![freeipa-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/freeipa-3.png)

![freeipa-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/freeipa-4.png)

![freeipa-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/freeipa-5.png)

```
# 关闭防火墙
setenforce 0
systemctl stop firewalld.service

# 访问https://ipa.bloodzer0.com
# 如果你是外部访问，记得在hosts文件中添加解析
# 用户名：admin 密码：你之前设置的密码
```

![freeipa-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/freeipa-6.png)

![freeipa-7](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/freeipa-7.png)
