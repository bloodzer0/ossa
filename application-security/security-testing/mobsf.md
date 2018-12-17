## MobSF概述
移动安全框架，支持：静态分析APK、IPA、APPX文件，动态分析APK与分析Web API。

[github地址](https://github.com/MobSF/Mobile-Security-Framework-MobSF)

## 安装与使用
### Centos7安装MobSF
```
# 安装java环境
yum install java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64 -y

# 安装python3
yum install https://centos7.iuscommunity.org/ius-release.rpm -y
yum install python36u.x86_64 python36u-pip.noarch python36u-devel.x86_64 -y

# 安装MobSF
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git && cd Mobile-Security-Framework-MobSF
pip3.6 install -r requirements.txt

# 启动MobSF
python3.6 manage.py runserver 0.0.0.0:8080
```

## 踩坑记
### 导出报告报错
```
Don't Play Around. An Error just popped in!
Attribute not found.

'bool' object has no attribute 'dumps'
```

[解决方案](https://github.com/MobSF/Mobile-Security-Framework-MobSF/issues/208)
