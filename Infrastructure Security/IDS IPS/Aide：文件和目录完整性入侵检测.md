## 概述
AIDE（Advanced Intrusion Detection Environment）：高级入侵检测环境

[官网地址](http://aide.sourceforge.net/)

[下载地址](https://sourceforge.net/projects/aide/files/)

## 安装
* Centos7下直接使用yum安装

```
yum install aide.x86_64 -y
```

## 使用
```
# 初始化数据库
aide -i

# 将初始化数据库改名为默认数据库
mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz

# 检查
aide -C

# 更新数据库，更新完成以后需要将新的数据库文件复制为数据库
aide -u
mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz
```

## 配置
```
vim /etc/aide.conf

# 文件属性
p:权限
i:节点
n:链接数
u:用户
g:组
s:大小
S:文件增长大小
b:块数量
m:修改时间
a:访问时间
c:创建时间

# 编译支持
acl:
selinux:
xattrs:

# 摘要算法
md5:
sha1:
sha256:
sha512:
rmd160:
tiger:
crc32:
whirlpool:
haval:
gost:
```
