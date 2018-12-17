# ModSecurity实现waf

## 安装与使用
```
# 操作系统：Centos7
# 解决依赖环境
yum install gcc.x86_64 libxml2-devel.x86_64 httpd.x86_64 httpd-devel.x86_64 pcre2.x86_64 pcre2-devel.x86_64 -y

# 安装mod_security
yum install mod_security.x86_64 -y

# 查看http 模块，结果如图
httpd -M | grep security
```

![modsecurity-waf-1](https://github.com/bloodzer0/ossa/raw/master/application-security/security-defense/img/modsecurity-waf-1.png)

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

![modsecurity-waf-2](https://github.com/bloodzer0/ossa/raw/master/application-security/security-defense/img/modsecurity-waf-2.png)

```
# 测试url：http://10.10.10.5/<script>

# 查看日志
tail -n 1 -f /etc/httpd/logs/error_log 
```

![modsecurity-waf-3](https://github.com/bloodzer0/ossa/raw/master/application-security/security-defense/img/modsecurity-waf-3.png)

```
# 查看模块日志
tail -f /var/log/httpd/modsec_audit.log
```

![modsecurity-waf-4](https://github.com/bloodzer0/ossa/raw/master/application-security/security-defense/img/modsecurity-waf-4.png)

## ModSecurity控制台waf-fle
[官网地址](http://www.waf-fle.org/)

[在线测试地址](http://demo.waf-fle.org/waf-fle/)

```
测试账号：waffle-demo
测试密码：waffle-demo
```

## 参考资料
[owasp mod_security](https://github.com/SpiderLabs/owasp-modsecurity-crs)
