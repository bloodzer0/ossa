[下载地址](https://sourceforge.net/projects/aide/files/aide/)

# 安装
```
yum install aide.x86_64 -y
```

# 使用
```
# 初始化数据库
aide -i
mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz

# 检查
aide -C

# 更新数据库，更新完成以后需要将新的数据库文件复制为数据库
aide -u
mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz
```
