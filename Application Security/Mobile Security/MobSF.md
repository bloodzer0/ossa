# 概述
[github地址](https://github.com/MobSF/Mobile-Security-Framework-MobSF)

## 功能
* 静态分析Android APK
* 静态分析IOS IPA
* 静态分析Windows APPX
* 动态分析Android APK
* Web API Fuzzer

# 安装
操作系统：Centos7
## 解决依赖环境
```
# 安装java环境
yum install java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64 -y

# 安装python3
yum install https://centos7.iuscommunity.org/ius-release.rpm -y

yum install python36u.x86_64 python36u-pip.noarch -y

# 解决模块
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git && cd Mobile-Security-Framework-MobSF
pip3.6 install -r requirements.txt
```

## 启动
```
python3.6 manage.py runserver

python3.6 manage.py runserver 0.0.0.0:8080
```

# 使用
