## 概述
[github地址](https://github.com/desaster/kippo)

## 安装
### 解决依赖环境
```
yum install epel-release -y
yum install python2-pip.noarch python-devel.x86_64 gcc.x86_64 -y

pip install Twisted # 这里有坑
pip install pycrypto
pip install zope.interface
pip install cryptography
pip install pyasn1
```

### 踩坑
* 执行start.sh报错

```
No module named kippo.core.config
```

解决方案：

```
pip install twisted==15.2.0
```

### 安装kippo

```
# 下载
git clone https://github.com/desaster/kippo.git

# 执行start.sh，备注：不能以root用户执行
cp kippo.cfg.dist kippo.cfg
./start.sh
```

![kippo-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/kippo-1.png)

效果图

![kippo-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/kippo-2.png)

![kippo-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/kippo-3.png)

## 使用
### 让kippo转发到22端口
```
iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
```
