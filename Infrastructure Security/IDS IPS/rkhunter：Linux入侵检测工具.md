## 概述
[官网地址](http://rkhunter.sourceforge.net/)

[下载地址](https://sourceforge.net/projects/rkhunter/files/rkhunter/)

## 安装
```
wget https://nchc.dl.sourceforge.net/project/rkhunter/rkhunter/1.4.6/rkhunter-1.4.6.tar.gz

tar -xf rkhunter-1.4.6.tar.gz

cd rkhunter-1.4.6

./installer.sh --install
```

## 使用
```
# 检查
rkhunter --check

# 检查 不需要用户执行enter进行确认
rkhunter --check --skip-keypress
```
