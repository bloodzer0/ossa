## 概述
[官网地址](https://konghq.com/)

## 安装
[官方安装文档](https://docs.konghq.com/install/centos/?_ga=2.210441301.1977415201.1542346890-1904609295.1542346890)

操作系统：Centos7

```
wget https://bintray.com/kong/kong-community-edition-rpm/download_file?file_path=centos/7/kong-community-edition-0.14.1.el7.noarch.rpm
rpm -ivh kong-community-edition-0.14.1.el7.noarch.rpm

yum install epel-release -y

# 配置数据库，我这里使用的是PostgreSQL
create user kong;
create database kong owner kong;
```

![kong-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Security%20Development%20Lifecycle/img/kong-1.png)

```
# kong迁移
kong migrations up
```

**报错处理记录**
最好是在[github issue](https://github.com/Kong/kong/issues)进行搜索

* 报错1

```
Error: [postgres error] could not retrieve current migrations: [postgres error] FATAL: Ident authentication failed for user "kong"
```

解决方案：

```
vim /var/lib/pgsql/data/pg_hba.conf # 我是centos7 所以文件位置在这里，如果你不是可以使用find查找一下

# 修改完成后重启服务
```

![kong-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Security%20Development%20Lifecycle/img/kong-2.png)

* 报错2

```
Error: [postgres error] could not retrieve current migrations: [postgres error] ERROR: function to_regclass(unknown) does not exist (8)
```

解决方案：

```
# 报这个错误意味着PostgreSQL版本不适配，所以重新安装PostgreSQL吧。Kong要求9.5+
# Centos7 PostgreSQL10的yum源：https://download.postgresql.org/pub/repos/yum/10/redhat/rhel-7-x86_64/pgdg-centos10-10-2.noarch.rpm
```
迁移完成后，启动kong

```
kong start

# 测试kong
curl -i http://localhost:8001

# kong提供外网访问
cp /etc/kong/kong.conf.default /etc/kong/kong.conf
vim /etc/kong/kong.conf
	admin_listen = 0.0.0.0:8001, 0.0.0.0:8444 ssl

# 重启kong
```
