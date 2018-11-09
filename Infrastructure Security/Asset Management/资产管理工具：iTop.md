## 概述
[官网地址](https://sourceforge.net/projects/itop/)

## 安装
操作系统：Centos7

```
yum install epel-release -y

# 安装php5.6yum源
rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

yum install httpd.x86_64 httpd-devel.x86_64 mariadb.x86_64 mariadb-server.x86_64 mariadb-devel.x86_64 -y

# 配置数据库

# 安装php以及php的扩展
yum install --enablerepo=remi --enablerepo=remi-php56 php.x86_64 php-soap.x86_64 php-xml.x86_64 php-mysqlnd.x86_64 php-gd.x86_64 php-mcrypt.x86_64 php-ldap.x86_64 -y

# 下载iTop
wget https://jaist.dl.sourceforge.net/project/itop/itop/2.5.1/iTop-2.5.1-4123.zip

# 解压
unzip iTop-2.5.1-4123.zip

# 移动到web目录，并修改权限
mv web /var/www/html/
chown -R apache:apache web
```

![iTop-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Asset%20Management/img/iTop-1.png)

## 使用

## 参考资料
[iTop系统使用手册](https://www.jianshu.com/p/94a115746514)
