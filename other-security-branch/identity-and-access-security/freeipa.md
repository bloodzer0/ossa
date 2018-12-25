# 基于Linux开源身份认证体系
[官网地址](https://www.freeipa.org/)

## 安装与使用
### Centos7安装FreeIPA
[官方安装文档](https://access.redhat.com/products/identity-management#getstarted)

```
# 配置一个DNS解析记录
echo 10.10.10.5 ipa.bloodzer0.com ipa >> /etc/hosts
echo ipa.bloodzer0.com > /etc/hostname

# 安装ipa-server
yum install ipa-server.x86_64 ipa-server-dns.noarch -y

# 开始配置，配置在下文
ipa-server-install

# 关闭防火墙
setenforce 0
systemctl stop firewalld.service

# 访问https://ipa.bloodzer0.com
# 如果你是外部访问，记得在hosts文件中添加解析
# 用户名：admin 密码：你之前设置的密码
```

* **安装过程中的配置**

```
Do you want to configure integrated DNS (BIND)? [no]: yes

Server host name [ipa.bloodzer0.com]: ipa.bloodzer0.com

Please confirm the domain name [bloodzer0.com]: bloodzer0.com

Please provide a realm name [BLOODZER0.COM]: BLOODZER0.COM

Directory Manager password: # 输入你的密码（活动目录）
Password (confirm):  # 确认你的密码

IPA admin password: # ipa admin密码
Password (confirm): # 确认密码

Do you want to configure DNS forwarders? [yes]: yes

Do you want to configure these servers as DNS forwarders? [yes]: yes

Enter an IP address for a DNS forwarder, or press Enter to skip: # 直接回车

Do you want to search for missing reverse zones? [yes]: yes
Do you want to create reverse zone for IP 10.10.10.5 [yes]: yes
Please specify the reverse zone name [10.10.10.in-addr.arpa.]: 10.10.10.in-addr.arpa.

Continue to configure the system with these values? [no]: yes

# 配置完成，等待安装
```

访问Web界面

![freeipa-1](https://github.com/bloodzer0/ossa/raw/master/other-security-branch/identity-and-access-security/img/freeipa-1.png)

### freeipa使用
```
# 查看信息
ldapsearch -H ldap://ipa.bloodzer0.com -x -D 'cn=Directory Manager' -W -LLL -Z '(uid=admin)'
```

## 踩坑记
### 安装报错
```
[error] CalledProcessError: Command '/bin/systemctl start certmonger.service' returned non-zero exit status 1
```

**解决方案**

```
# 在yum install ipa-server.x86_64 ipa-server-dns.noarch -y这一步后执行如下操作

systemctl restart dbus.service
```
