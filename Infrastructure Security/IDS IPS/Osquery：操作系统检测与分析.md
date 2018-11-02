## 概述
[官网地址](https://osquery.io/)

## 安装
[官方安装文档](https://osquery.readthedocs.io/en/stable/installation/install-linux/)

操作系统：Centos7

```
# 安装yum源
rpm -ivh https://osquery-packages.s3.amazonaws.com/centos7/noarch/osquery-s3-centos7-repo-1-0.0.noarch.rpm

# 安装
yum install osquery.x86_64 -y

# 配置
cp /usr/share/osquery/osquery.example.conf /etc/osquery/osquery.conf

# 启动
systemctl start osqueryd

# 执行
osqueryi
```

## 使用
### 配置文件
```
vim /etc/osquery/osquery.conf
```

## 参考资料
[企业安全建设之主机资产管理与分析](http://www.freebuf.com/articles/security-management/127851.html)
