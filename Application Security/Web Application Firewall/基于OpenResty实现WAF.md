## 概述

## 安装与使用
操作系统：Centos7

### 安装OpenResty

```
# 解决依赖
yum install gcc.x86_64 gcc-c++.x86_64 openssl-devel.x86_64 pcre-devel.x86_64 -y

# 下载OpenResty
wget https://openresty.org/download/openresty-1.13.6.2.tar.gz

# 安装OpenResty
tar -xf openresty-1.13.6.2.tar.gz && cd openresty-1.13.6.2
./configure
gmake && gmake install
/usr/local/openresty/nginx/sbin/nginx # 启动服务

# 测试OpenResty，编写测试文件(详情如图)
vim /usr/local/openresty/nginx/conf/nginx.conf

        location /hello {
            default_type text/html;
            content_by_lua_block {
                ngx.say("hello world")
            }
        }

# 检查nginx配置文件并重启
/usr/local/openresty/nginx/sbin/nginx -t
/usr/local/openresty/nginx/sbin/nginx -s reload

# 访问测试文件，结果如图
```

![waf-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Application%20Security/Web%20Application%20Firewall/img/waf-1.png)

![waf-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Application%20Security/Web%20Application%20Firewall/img/waf-2.png)

### 安装unixhot waf

```
# 下载waf
git clone https://github.com/unixhot/waf

# 复制到对应目录
mkdir /usr/local/openresty/nginx/conf/waf
cp -r waf/waf/* /usr/local/openresty/nginx/conf/waf

# 创建waf日志目录
mkdir /tmp/waf-log && chmod 777 /tmp/waf-log

# 修改waf配置，习惯日志目录
vim /usr/local/openresty/nginx/conf/waf/config.lua

	config_log_dir = "/tmp/waf-log"
```

### 配置waf
```
vim /usr/local/openresty/nginx/conf/nginx.conf

    lua_shared_dict limit 50m;
    lua_package_path "/usr/local/openresty/nginx/conf/waf/?.lua";
    init_by_lua_file "/usr/local/openresty/nginx/conf/waf/init.lua";
    access_by_lua_file "/usr/local/openresty/nginx/conf/waf/access.lua";

# 重新载入配置文件    
/usr/local/openresty/nginx/sbin/nginx -s reload
```

![waf-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Application%20Security/Web%20Application%20Firewall/img/waf-3.png)

找一个测试页面，测试结果如下:

![waf-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Application%20Security/Web%20Application%20Firewall/img/waf-4.png)

## 规则学习
waf规则文件目录：/usr/local/openresty/nginx/conf/waf/rule-config

## 开源waf
[jx-sec 锦衣盾](https://github.com/jx-sec/jxwaf)

[unixhot waf](https://github.com/unixhot/waf)

[loveshell&nginx lua waf](https://github.com/loveshell/ngx_lua_waf)
