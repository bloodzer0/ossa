[官网地址](http://www.modsecurity.org/)

## 概述

## 安装与使用
操作系统：Centos7

```
# 解决依赖环境
yum install gcc.x86_64 libxml2-devel.x86_64 httpd.x86_64 httpd-devel.x86_64 pcre2.x86_64 pcre2-devel.x86_64 -y

# 安装mod_security
yum install mod_security.x86_64 -y

# 查看http 模块，结果如图
httpd -M
```

![waf-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Web%20Application%20Firewall/img/waf-5.png)

```
# 启动服务
systemctl start httpd.service

# mod_security配置文件
/etc/httpd/conf.d/mod_security.conf

# 规则目录
/etc/httpd/modsecurity.d/activated_rules

# 安装mod_security规则
yum -y install mod_security_crs

# 查看规则
```

![waf-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Web%20Application%20Firewall/img/waf-6.png)

访问测试，测试结果如图：

```
tail -f /etc/httpd/logs/error_log 
```

![waf-7](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Web%20Application%20Firewall/img/waf-7.png)


再查看其他日志，测试结果如图：

```
tail -f /var/log/httpd/modsec_audit.log
```

![waf-8](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Web%20Application%20Firewall/img/waf-8.png)

## 参考资料
[owasp mod_security](https://github.com/SpiderLabs/owasp-modsecurity-crs)
