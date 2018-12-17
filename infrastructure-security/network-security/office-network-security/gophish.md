## gophish概述
开源钓鱼工具：支持配置钓鱼邮件模板、公司员工邮箱信息等等；

[github地址](https://github.com/gophish/gophish)

## 安装与使用
### Centos7安装gophish
```
# 下载
wget https://github.com/gophish/gophish/releases/download/0.7.1/gophish-v0.7.1-linux-64bit.zip

# 解压并启动
mkdir -p /app/gophish
unzip gophish-v0.7.1-linux-64bit.zip -d /app/gophish/ && cd /app/gophish/

# 修改配置文件，修改监听地址

```

![gophish-1](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-1.png)

```
# 启动gophish
./gophish

# 访问：https://10.10.10.5:3333
# 用户名：admin 密码：gophish
# 至此安装完成
```

![gophish-2](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-2.png)

### 基础使用
* **配置Users & Groups**

![gophish-3](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-3.png)

* **配置Email Templates**

![gophish-4](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-4.png)

* **配置Landing Pages**

![gophish-5](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-5.png)

* **配置Sending Profiles**

![gophish-6](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-6.png)

![gophish-7](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-7.png)

![gophish-8](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-8.png)

## 使用gophish获取用户输入的账号密码
* **配置一个用于钓鱼的页面**

```
# 我这里随机找了一个登录站点用于测试
# http://xnore.com/admin/login.php
# 点击import site，填写如上的网址
# 勾选Capture Submitted Data
# 勾选Capture Passwords
# Redirect to填写：http://xnore.com/admin/login.php
```

![gophish-9](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-9.png)

* **配置邮件的模版**

```
# 构造一封用于钓鱼的邮件
# 查看邮件源文件，复制源文件信息
# 在邮件模版选择import email
```

![gophish-10](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-10.png)

![gophish-11](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-11.png)

![gophish-12](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-12.png)

* **配置Campaigns**

![gophish-13](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-13.png)

```
# 员工访问邮件
# 员工通过邮件中的地址输入账号密码
```

![gophish-14](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-14.png)

![gophish-15](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-15.png)

![gophish-16](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/network-security/img/gophish-16.png)

## 使用心得
### 优点
* 能够进行企业内部员工安全意识培训起到很好的辅助作用；
* 如果条件允许的情况，可以结合MSF进行PC内网渗透；

### 缺点
* 不支持员工邮箱导入，如果需要进行"海钓"需要一个一个添加，手工活，不过可以通过脚本来实现；
* 获取的用户信息只包括操作系统版本、浏览器版本、以及用户提交的数据，不能精准定位到是哪个用户点击了钓鱼链接；

## 参考资料
[Automate Phishing Emails with GoPhish](https://www.youtube.com/watch?v=knc6Iq-hNcw)
