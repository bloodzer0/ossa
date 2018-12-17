# OpenResty实现waf

## 安装与使用
```
# 操作系统：centos7
# 解决依赖
yum install gcc.x86_64 gcc-c++.x86_64 openssl-devel.x86_64 pcre-devel.x86_64 -y

# 下载OpenResty
wget https://openresty.org/download/openresty-1.13.6.2.tar.gz

# 安装OpenResty
tar -xf openresty-1.13.6.2.tar.gz && cd openresty-1.13.6.2
./configure
gmake && gmake install

# 启动服务
/usr/local/openresty/nginx/sbin/nginx

# 停止服务
/usr/local/openresty/nginx/sbin/nginx -s quit

# 测试OpenResty
vim /usr/local/openresty/nginx/conf/nginx.conf
```

```
location /hello {
    default_type text/html;
    content_by_lua_block {
        ngx.say("hello world")
    }
}
```

![openresty-waf-1](https://github.com/bloodzer0/ossa/raw/master/application-security/security-defense/img/openresty-waf-1.png)

```
# 重新载入配置文件    
/usr/local/openresty/nginx/sbin/nginx -s reload

# 访问页面：http://10.10.10.5/hello
```

![openresty-waf-2](https://github.com/bloodzer0/ossa/raw/master/application-security/security-defense/img/openresty-waf-2.png)

## 实现waf
```
# 下载waf
git clone https://github.com/unixhot/waf

# 复制到对应目录
mkdir /usr/local/openresty/nginx/conf/waf
cp -r waf/waf/* /usr/local/openresty/nginx/conf/waf

# 创建waf日志目录
mkdir /tmp/waf-log && chmod 777 /tmp/waf-log

# 修改waf配置，修改日志目录
vim /usr/local/openresty/nginx/conf/waf/config.lua
```

```
config_log_dir = "/tmp/waf-log"
```

### 配置waf
```
vim /usr/local/openresty/nginx/conf/nginx.conf
```

```
lua_shared_dict limit 50m;
lua_package_path "/usr/local/openresty/nginx/conf/waf/?.lua";
init_by_lua_file "/usr/local/openresty/nginx/conf/waf/init.lua";
access_by_lua_file "/usr/local/openresty/nginx/conf/waf/access.lua";
```

![openresty-waf-3](https://github.com/bloodzer0/ossa/raw/master/application-security/security-defense/img/openresty-waf-3.png)

```
# 重启服务
# 访问测试页面：http://10.10.10.5/hello.sql
```

![openresty-waf-4](https://github.com/bloodzer0/ossa/raw/master/application-security/security-defense/img/openresty-waf-4.png)

## waf的说明
### 规则说明
waf规则文件目录：/usr/local/openresty/nginx/conf/waf/rule-config

规则文件说明：可以在相应的规则中添加对应的值

```
args.rule：参数值规则
blackip.rule：黑名单IP
cookie.rule：Cookie值规则
post.rule：Post数据规则
url.rule：URL规则
useragent.rule：User-Agent规则
whiteip.rule：白名单IP
whiteurl.rule：白名单URL
```

### 配置说明
```
# waf的状态，on为开启，off为关闭
config_waf_enable = "on"

# 日志文件路径
config_log_dir = "/tmp/waf-log"

# 规则文件路径
config_rule_dir = "/usr/local/openresty/nginx/conf/waf/rule-config"

# 是否启用白名单url检测
config_white_url_check = "on"

# 是否启用白名单ip检测
config_white_ip_check = "on"

# 是否启用黑名单ip检测
config_black_ip_check = "on"

# 是否启用url检测
config_url_check = "on"

# 是否启用参数检测
config_url_args_check = "on"

# 是否启用user-agent检测
config_user_agent_check = "on"

# 是否启用cookie检测
config_cookie_check = "on"

# 是否启用cc检测
config_cc_check = "on"

# CC检测频率设置，num/time
config_cc_rate = "10/60"

# 是否启用post数据检测
config_post_check = "on"
```

根据配置可得，waf规则的优先级为：白名单--黑名单--规则检测（allow>deny）

## 参考资料
### 其它开源waf
* [loveshell&nginx lua waf](https://github.com/loveshell/ngx_lua_waf)
* [jx-sec 锦衣盾](https://github.com/jx-sec/jxwaf)
