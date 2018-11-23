## 概述
[官网地址](http://www.jumpserver.org/)

## 安装
[安装官方文档](http://docs.jumpserver.org/zh/docs/step_by_step.html)

操作系统：Centos7

```
# 关闭防火墙与SELinux
systemctl disable firewalld.service
systemctl stop firewalld.service

vim /etc/selinux/config

# 修改字符集，否则可能报 input/output error的问题，因为日志里打印了中文。
localedef -c -f UTF-8 -i zh_CN zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
echo 'LANG="zh_CN.UTF-8"' > /etc/locale.conf
```

### 准备python3和虚拟环境
```
yum install epel-release gcc.x86_64 -y
yum install python36.x86_64 python36-devel.x86_64 -y

# 建立python虚拟环境
cd /opt/
python3.6 -m venv py3
source /opt/py3/bin/activate

# 自动载入python虚拟环境配置（个人觉得没有必要）
```

### 安装Jumpserver
```
git clone https://github.com/jumpserver/jumpserver.git

# 添加环境变量
echo "source /opt/py3/bin/activate" > /opt/jumpserver/.env

# 激活py3环境
cd /opt/jumpserver && source .env

# 解决环境依赖
cd /opt/jumpserver/requirements
yum install $(cat rpm_requirements.txt) -y # rpm包依赖

pip install --upgrade pip setuptools
pip install -r requirements.txt # python库依赖

# 安装Redis
yum install redis.x86_64 -y
systemctl start redis.service

# 安装MySQL
yum install mariadb-devel.x86_64 mariadb-libs.x86_64 mariadb-server.x86_64 mariadb.x86_64 -y

# 配置数据库
# 数据库：jumpserver
create database jumpserver default charset 'utf8';

# 修改jumpserver配置文件
cd /opt/jumpserver/ && cp config_example.py config.py
	# 修改数据库部分，按照官网来进行修改

# 初始化数据库
cd /opt/jumpserver/utils/
bash make_migrations.sh

# 运行Jumpserver
cd /opt/jumpserver
./jms start all # 后台运行指定-d参数

# 访问：admin/admin
```

![jumpserver-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Host%20Security/Fortress%20Machine/img/jumpserver-1.png)

### 安装SSH Server和WebSocket Server
```
# 激活python3环境
cd /opt
git clone https://github.com/jumpserver/coco.git
echo "source /opt/py3/bin/activate" > /opt/coco/.env

# 安装依赖
cd /opt/coco/requirements/
yum install $(cat rpm_requirements.txt) -y
pip install -r requirements.txt

# 修改配置文件
cd /opt/coco
mkdir keys logs
cp conf_example.py conf.py

vim conf.py # 按照官网进行配置

# 启动
cd /opt/coco/
./cocod start # -d 后台运行
# 停止任务 ./cocod stop
```

访问Jumpserver接受coco注册

![jumpserver-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Host%20Security/Fortress%20Machine/img/jumpserver-2.png)

此时已经可以使用Jumpserver了

```
ssh -p2222 admin@Jumpserver-Host # 密码：admin
```

![jumpserver-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Host%20Security/Fortress%20Machine/img/jumpserver-3.png)

### 安装Web Terminal前端：Luna
```
cd /opt
wget https://github.com/jumpserver/luna/releases/download/v1.4.4/luna.tar.gz
tar xvf luna.tar.gz
chown -R root:root luna
```

### 安装Nginx

## 添加资产
### 添加资产的流程
* 创建一个管理用户：管理用户是服务器的 root,或拥有 NOPASSWD: ALL sudo 权限的用户,Jumpserver 使用该用户来推送系统用户、获取资产硬件信息等。


* 创建一个系统用户：系统用户是 Jumpserver 跳转登录资产时使用的用户,可以理解为登录资产用户,如 web, sa, dba。（此用户需要在系统初始化的时候安装好）

* 添加用户：此用户用于登录jumpserver
