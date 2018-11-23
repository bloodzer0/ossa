## 概述
### 10大注意事项
* 浏览器拦截混合访问请求
* 前端HTML、JS资源引用存在HTTP
* 移动端适配HTTPS
* 项目中存在的配置问题
* 关于request.getScheme()的坑
* SSL证书类型
* Nginx配置同一个Server段不支持配置多个证书
* Nginx配置HTTP强制跳转HTTPS
* 所有环节均要进行升级
* 打死你都想不到的地方

```
兵来将挡，水来土掩
```

### 常见问题
* **HSTS：HTTP Strict Transport Security**

如果一个网站既提供了HTTP服务，又提供了HTTPS服务(在过渡期通常如此)，怎样引导用户访问HTTPs的站点呢？这就是HSTS(HTTP Strict Transport Security)的作用。通过Web服务器上的设置，在收到HTTP访问请求时，返回的Header里带有Strict-Transport-Security字段，告知浏览器必须使用HTTPs进行访问。

但是，HSTS并不能避免首次跳转时遇到的劫持。要彻底解决这个问题，可以申请加入Preload List(预加载列表)。

Preload List是由Google Chrome维护的“HTTPS站点列表”，Chrome, Firefox, Safari, Edge, IE 11均在使用。一旦浏览器发现要访问的站点在Preload List上，默认就会发起HTTPs链接。这样，就避免了HSTS的首次跳转被劫持的隐患。

* **SSL卸载**

通常的方案里，HTTPS的加密传输只限于客户端出发的公网阶段，在内网的通讯流量仍然采用非加密的HTTP传输。这种"把加密流量转换为非加密流量"的过程，就是常说的SSL/TLS卸载(Offloading，以下简称“SSL卸载”)。

有一些公司会采用F5来做负载均衡，F5应付单纯的L4和L7的流量是没有问题的，但进行SSL卸载时性能往往会急剧降低，F5可以提供专门的加速卡来解决这个问题，但价格不便宜。所以，还需要专门环节来进行SSL卸载，常见的Nginx和HAProxy都可以执行这个任务。

2010年Intel出品的Westmere系列处理器之后，CPU支持AES-NI(Advanced Encryption Standard New Instructions)指令集，可以极大提高软件进行SSL加解密的速度(通常的数据是5倍左右)。但是，单纯采用CPU并不会直接享受这种好处，还需要对应的OpenSSL提供支持。想要知道自己的OpenSSL是否利用了AES-NI加速，可以用OpenSSL的命令行调试，加上-evp参数，测试速度是否有明显变化即可。

```
openssl speed aes-256-cbc
openssl speed -evp AES256
```

* **客户端证书**

* **服务器端证书**

* **SNI**

* **CDN**

* **内容及其它**

## 参考资料
[全站 HTTPS 没你想象的那么简单](https://www.cnblogs.com/mafly/p/allhttps.html)

[全站HTTPS的那些坑](http://lusongsong.com/info/post/8930.html?from=kaodertimeline)

[启用全站HTTPS后不仅更安全而且更快 看淘宝是如何做到的](https://mp.weixin.qq.com/s?__biz=MzAxNDEwNjk5OQ==&mid=402402866&idx=1&sn=f3fde8ece13d51397c12f1a08713ebeb&scene=0#wechat_redirect)
