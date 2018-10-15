# 安装Jenkins
操作系统：Centos7

[官网地址](https://jenkins.io/)

[下载地址](https://pkg.jenkins.io/redhat/)

## 解决依赖环境
```
yum install java-1.8.0-openjdk.x86_64 -y
```

## 安装
```
rpm -ivh jenkins-2.147-1.1.noarch.rpm

# 启动
service jenkins start

# 访问
http://192.168.199.5:8080
```

# 安装Sonar
见Sonar.md文档

# 安装插件
[官网地址](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+Jenkins)

## 使用Jenkins安装插件
系统管理--插件管理--可选插件

![sonar-7](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Code%20Audit/img/sonar-7.png)

重启Jenkins

# 配置Sonar
系统配置--系统设置

需要勾选Enable injection of SonarQube server configuration as build environment variables（图片中没有勾选是个错误）

![sonar-8](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Code%20Audit/img/sonar-8.png)

## token的生成
访问sonar web界面，点击当前用户，我这里是administrator

![sonar-9](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Code%20Audit/img/sonar-9.png)

生成以后填入Jenkins配置信息中

系统配置--全局工具配置
![sonar-10](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Code%20Audit/img/sonar-10.png)

# 执行分析
构建项目的时候选择：Execute SonarQube Scanner

![sonar-11](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Code%20Audit/img/sonar-11.png)
