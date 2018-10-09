# 安装

[官网](https://sourceforge.net/projects/itop/
)

安装依赖环境（新版本的iTop需要php5.6及其以上）

```
yum install epel-release -y 

# 安装php5.6yum源
rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

yum install httpd.x86_64 httpd-devel.x86_64 mariadb.x86_64 mariadb-server.x86_64 mariadb-devel.x86_64 -y

yum install --enablerepo=remi --enablerepo=remi-php56 php56.x86_64 php56-php-soap.x86_64 php56-php-xml.x86_64 php56-php-mysqlnd.x86_64 php56-php-gd.x86_64 php56-php-mcrypt.x86_64 php56-php-ldap.x86_64 -y
```

启动服务

```
systemctl enable httpd.service mariadb.service
systemctl start httpd.service mariadb.service
```

关闭防火墙

```
vim /etc/selinux/config # 永久关闭selinux
    SELINUX=disabled
setenforce 0 # 临时关闭selinux，重启失效
systemctl disable firewalld.service
systemctl stop firewalld.service
```

安装iTop

```
wget https://nchc.dl.sourceforge.net/project/itop/itop/2.5.0/iTop-2.5.0-3935.zip
unzip iTop-2.5.0-3935.zip

# 修改权限
chown -R apache:apache web

mv web /var/www/html/
```

访问安装
