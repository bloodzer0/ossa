# 主机安全扫描
针对主机安全扫描主要包括：端口开放扫描与访问控制扫描、漏洞扫描与弱口令扫描。

针对企业内部安全扫描应该分为两种类型：内部扫描与外部扫描，这里的内化划分应该是网络分界的划分；内部扫描主要用于发现内部的安全隐患，外部扫描主要是站在攻击者的角度发现安全隐患。

## 端口扫描
端口扫描应该包括：操作系统信息扫描、端口开放扫描、服务探测(服务版本信息)等等。

### 端口扫描的思路
* 端口开放扫描：全端口扫描，快速扫描；
* 端口服务信息扫描：开放端口扫描，慢速扫描；

利用工具进行扫描的思路：

```
1.利用masscan进行全端口存活性扫描，由于masscan扫描可能不精准，可以扫描两次做并集操作；
2.利用nmap对masscan扫描出来的存活端口进行服务探测；
3.扫描结果入库，包括：IP、Port、Service、Service Banner、OS Banner等等
```

端口扫描的另类思路：利用主机入侵检测agent采集端口开放信息，这部分的信息与内部扫描的结果会有重合，也就是说我们可以利用主机入侵检测来获取内部端口扫描结果。


### 端口扫描的工具
* **nmap**

```
# centos7 安装 nmap
yum install nmap.x86_64 -y
```

nmap常用命令

```

```

* **masscn**

[github地址](https://github.com/robertdavidgraham/masscan)

```
# ubuntu/debian 安装 masscan
sudo apt-get install git gcc make libpcap-dev
git clone https://github.com/robertdavidgraham/masscan
cd masscan
make
```

```
# centos7 安装 masscan
# 下载地址：https://pkgs.org/download/masscan
# 利用下载的RPM包直接进行安装
wget http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/m/masscan-1.0.3-5.el7.x86_64.rpm
yum install libpcap-devel.x86_64 -y
rpm -ivh masscan-1.0.3-5.el7.x86_64.rpm

# yum进行安装
vim /etc/yum.repos.d/nux-misc.repo
```

nux-misc.repo文件内容

```
[nux-misc]
name=Nux Misc
baseurl=http://li.nux.ro/download/nux/misc/el7/x86_64/
enabled=0
gpgcheck=1
gpgkey=http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
```

```
# 安装
yum --enablerepo=nux-misc install masscan
```

* **zmap**

[github地址](https://github.com/zmap/zmap)

```
# centos7 安装 zmap
yum install zmap.x86_64 -y
```

## 漏洞扫描

## 弱口令扫描
弱口令扫描也是漏洞扫描的一种，只不过弱口令扫描算是比较特殊的一类漏洞。
