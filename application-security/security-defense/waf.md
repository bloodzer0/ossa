# Web应用防火墙
### waf的部署方式
* **根据部署逻辑**
主要分为串联与并联两种形式。

推荐选择：物理并联逻辑串联的部署方式，对业务的影响较低并容易做降级方案。

* **根据部署方式**
cname部署（代理模式）、module部署（主机模式）、网络层部署（网络模式）

### 企业上waf时的思考
* 单台waf能承受的QPS上限是多少？
* 部署waf对系统性能造成的影响是多少？
* waf的误报率与漏报率是否能达到预期的平衡值（平衡值：任何安全设备都保障不了低漏报、低误报，只能在误报与漏报之间均衡，达到心中的平衡就是最好的）？

## 参考资料
[互联网公司WAF系统设计](https://www.freebuf.com/articles/network/128370.html)

[使用OpenResty搭建WAF应用](https://www.jianshu.com/p/9526969b321e)

[使用NGINX+Openresty实现WAF功能](https://www.cnblogs.com/reblue520/p/6814072.html)
