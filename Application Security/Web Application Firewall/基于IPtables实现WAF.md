## 概述
iptables工作在主机层面，对于进出的网络或者主机的数据报文，根据事先设定好的检查规则对其检查，并作出相应的处理。

## 配置与使用
### iptables拦截ip
安装Apache进行测试

```
# 拦截ip
iptables -A INPUT -p tcp --dport 80 -s 192.168.199.1 -j DROP

# 删除拦截
iptables -D INPUT -p tcp --dport 80 -s 192.168.199.1 -j DROP
```

### 利用ipset进行批量IP拦截
```
# 创建ip集
ipset create bloodzer0 hash:ip

# 查看ip集
ipset list

# 添加成员
ipset add bloodzer0 ip

# 删除成员
ipset del bloodzer0 ip

# 将ipset保存为文件
ipset save bloodzer0 -f bloodzer0.txt

# iptables与ipset结合
iptables -A INPUT -m set --match-set bloodzer0 src -p tcp --dport 80  -j DROP 
```

### 问题思考
iptables封禁IP，是封禁的直连IP，也就是直接与服务器通信的IP。但是我们一般的网络架构是：CDN--SLB--Server 或者 CDN--Server，这个时候我们使用上面的方式就失效了，所以我们采用新的方式：根据XFF来封禁IP。

## 根据XFF+iptables封禁IP
```
iptables -A INPUT -p tcp --dport 80 -m string --algo kmp --string "X-Forwarded-For: IP" -j DROP
```

根据这种方式就不能使用ipset，所以如果是业务量很大的时候，不建议采用这种方式，因为一个IP一条规则，而iptables会针对每一个进来的数据包进行分析，我测试过一台2核16G的服务器，在规则数量达到2000以上的时候，业务量稍微一大，服务器CPU容易飙到90%以上。
