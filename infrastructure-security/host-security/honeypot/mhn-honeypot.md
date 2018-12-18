# MHN蜜罐

MHN（Modern Honey Network）：开源蜜罐，简化蜜罐的部署，同时便于收集和统计蜜罐的数据。用ThreatStream来部署，数据存储在MOngoDB中，安装了入侵检测系统的部署传感器Snort、Kippo、Conpot和Dionaea。收集的信息可以通过Web接口进行展示。

[github地址](https://github.com/threatstream/mhn)

### 支持蜜罐类型
* Snort
* Suricata
* Dionaea
* Conpot
* Kippo
* Amun
* Glastopf
* WordPot
* ShaockPot
* P0f

### MHN架构
![mhn-1](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/mhn-1.png)

## MHN安装与使用
### Ubuntu安装MHN
```
# 操作系统：ubuntu16.04

# 下载
git clone https://github.com/threatstream/mhn.git

# 安装过程有点久，需要安装很多软件包
cd mhn && sudo ./install.sh

# 配置服务器信息，这里的邮箱与密码就是安装完成后的Web后台密码
```

![mhn-2](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/mhn-2.png)

```
# 配置splunk与ELK，我这里选择不配置
```

![mhn-3](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/mhn-3.png)

### MHN配置
* **部署蜜罐**

在Deploy选项中，选择我们需要部署的蜜罐类型，复制部署脚本进行安装。

* **集成Splunk与ArcSight**

```
cd mhn/scripts/
sudo ./install_hpfeeds-logger-splunk.sh
sudo ./install_hpfeeds-logger-arcsight.sh

tail -f /var/log/mhn/mhn-splunk.log
tail -f /var/log/mhn/mhn-arcsight.log
```

* **禁止数据上报**

MHN Server会默认将分析数据上报给Anomali，如果需要禁用此配置，运行如下命令：

```
cd mhn/scripts/
sudo ./disable_collector.sh
```

## 利用MHN部署蜜罐
### SSH蜜罐测试

```
# 蜜罐部署机器：10.10.10.5
# 部署蜜罐：dionaea
wget "http://10.10.10.8/api/script/?text=true&script_id=4" -O deploy.sh && sudo bash deploy.sh http://10.10.10.8 pp8D4gPI
```

![mhn-4](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/mhn-4.png)

```
# 尝试暴力破解攻击
hydra -l root -P password.txt mssql://10.10.10.5
```

![mhn-5](https://github.com/bloodzer0/ossa/raw/master/infrastructure-security/host-security/img/mhn-5.png)

## 心得体会
* 蜜罐可以存在于各种场景之中（机房、内网、云环境）；
* 蜜罐产品的思考应该是在防火墙、入侵检测之后，属于提高企业安全水平的辅助产品；
* 蜜罐主要部署在企业内网，起到预警与了解攻击中入侵的情况的作用，如：发现员工PC中毒对内网的入侵、转移攻击者注意力等等；
* 蜜罐不能设置的太过简单，也不能设置太过复杂；

### 优点
* 内置集成大量的蜜罐系统，且提供一键部署蜜罐的方式；
* 支持蜜罐攻击效果图展示，与ArcSight或Splunk集成友好；

### 缺点
* 部署时间需要很长时间（不算缺点吧，网速给力情况下不存在这个问题）；
* 不支持对蜜罐的集中管理；

## 参考资料
[Build Your Own Honeypot Network In Under An Hour](https://jerrygamblin.com/2017/05/29/build-your-own-honeypot-network-in-under-an-hour/)
