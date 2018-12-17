# iptables实现waf
iptables工作在主机层面，对于进出的网络或者主机的数据报文，根据事先设定好的检查规则对其检查，并作出相应的处理。

## iptables实现waf
### iptables拦截ip
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
iptables封禁IP，是封禁的直连IP，也就是直接与服务器通信的IP。但是我们一般的网络架构是：CDN--SLB--Server或者CDN--Server，这个时候我们使用上面的方式就失效了，所以我们采用新的方式：根据XFF来封禁IP。

### 根据XFF+iptables封禁IP
```
iptables -A INPUT -p tcp --dport 80 -m string --algo kmp --string "X-Forwarded-For: IP" -j DROP
```

**备注：XFF很好伪造绕过。**

## 踩坑记
### XFF+iptables实现waf性能瓶颈
这种方式就不能使用ipset，所以如果是业务量很大的时候，不建议采用这种方式，因为一个IP一条规则，而iptables会针对INPUT每一个数据包进行解析判断。

我测试过一台2核16G的服务器，在规则数量达到2300+的时候，QPS稍微一大，服务器CPU容易飙到90%以上。

## 参考资料
