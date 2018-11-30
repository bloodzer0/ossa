## 概述
[github地址](https://github.com/threatstream/mhn)

MHN（Modern Honey Network）：开源蜜罐，简化蜜罐的部署，同时便于收集和统计蜜罐的数据。用ThreatStream来部署，数据存储在MOngoDB中，安装了入侵检测系统的部署传感器Snort、Kippo、Conpot和Dionaea。收集的信息可以通过Web接口进行展示。

支持蜜罐的完整列表：

```
Snort
Suricata
Dionaea
Conpot
Kippo
Amun
Glastopf
Wordpot
ShockPot
P0f
```

### MHN架构
![mhn-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/mhn-5.png)

## 安装
操作系统：Ubuntu18.04（推荐在Ubuntu上安装）

```
git clone https://github.com/threatstream/mhn.git

cd mhn && sudo ./install.sh # 安装过程有点久，需要安装很多软件包

# 一直安装到需要配置一些信息
```

![mhn-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/mhn-1.png)

后续还会配置Splunk以及ELK的东西(也可以选择不安装，我未安装)，直至安装完成：登录的账号密码就是之前在安装过程中配置的邮箱与密码。

![mhn-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/mhn-2.png)

![mhn-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/mhn-3.png)

## 配置
### 部署蜜罐
在菜单栏的Deploy选项中，可以部署多种类型的的脚本进行部署。我们随意选择一种，复制Deploy Command到蜜罐机器中进行执行，执行完成后，查看Sensors：(安装也是一个漫长的过程)

![mhn-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/mhn-4.png)

### 集成Splunk与ArcSight
```
cd mhn/scripts/
sudo ./install_hpfeeds-logger-splunk.sh
sudo ./install_hpfeeds-logger-arcsight.sh

tail -f /var/log/mhn/mhn-splunk.log
tail -f /var/log/mhn/mhn-arcsight.log
```

### 禁用数据传递
MHN Server会默认将分析数据上报给Anomali，如果需要禁用此配置，运行如下命令：

```
cd mhn/scripts/
sudo ./disable_collector.sh
```

## 测试
### 测试SSH蜜罐
```
# 找一台安装了SSH服务的机器

# 下载cowrie脚本
wget "http://192.168.199.7/api/script/?text=true&script_id=5" -O deploy.sh && sudo bash deploy.sh http://192.168.199.7 GGe5wBNN

# 查看此时机器的服务端口，此时多了一个2222端口
netstat -tulnp
```

![mhn-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/mhn-6.png)

```
# 模拟攻击后的效果图
```

![mhn-7](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Honeypot/img/mhn-7.png)

## 参考资料
[Build Your Own Honeypot Network In Under An Hour](https://jerrygamblin.com/2017/05/29/build-your-own-honeypot-network-in-under-an-hour/)
