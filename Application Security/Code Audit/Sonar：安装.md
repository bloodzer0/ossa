[官网地址](https://www.sonarqube.org/)

[官方文档](https://docs.sonarqube.org/display/SONAR/Documentation/)

# 安装
[下载最新版本](https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.3.zip)

操作系统：Centos7

## 解决环境依赖
**不要用Centos自带的MariaDB**

使用MariaDB会在配置数据库后，导致连接不上

```
# 安装jvm环境
yum install java-1.8.0-openjdk.x86_64 -y

# 安装数据库
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

rpm -ivh mysql57-community-release-el7-10.noarch.rpm

yum install mysql-community-server -y

# 配置数据库
systemctl start mysqld.service

# 数据库初始密码
grep "password" /var/log/mysqld.log

# 修改数据库密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';

# 创建数据库sonar
create database sonar;
```

## 开始安装
**不要用root进行安装否则会报错**

报错原因：以root安装，es会报错“can not run elasticsearch as root”

```
# 解压
unzip sonarqube-7.3.zip

# 启动
./sonarqube-7.3/bin/linux-x86-64/sonar.sh start

# 访问
http://localhost:9000

账号：admin	密码：admin
```

![sonar-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Code%20Audit/img/sonar-1.png)

## 配置数据库
```
vim ./sonarqube-7.3/conf/sonar.properties
```

![sonar-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Code%20Audit/img/sonar-2.png)

## 汉化
在Administration菜单栏，选择Marketplace标题，在搜索框输入chinese进行搜索，然后进行安装

![sonar-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Code%20Audit/img/sonar-3.png)

安装完成后重启

![sonar-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Code%20Audit/img/sonar-4.png)
