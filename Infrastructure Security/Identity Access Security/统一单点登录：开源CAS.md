## 概述

[github地址](https://github.com/apereo/cas)

## 安装
### 安装环境
备注：环境变量在最后统一配置

* JAVA环境安装

```
# 下载并解压到指定文件
tar -xf jdk-8u191-linux-x64.tar.gz -C /app/
```

* Apache Maven环境安装

```
# 下载并解压到指定文件
tar -xf apache-maven-3.6.0-bin.tar.gz -C /app/
```

* Apache Tomcat环境安装

```
# 下载并解压到指定文件
tar -xf apache-tomcat-8.5.35.tar.gz -C /app/

# 测试tomcat，需要先配置环境变量
/app/apache-tomcat-8.5.35/bin/startup.sh

# 访问
http://hostname:8080/
```

![cas-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-1.png)

* 配置环境变量

```
# vim /etc/profile
	export JAVA_HOME=/app/jdk1.8.0_191
	export MAVEN_HOME=/app/apache-maven-3.6.0
	export PATH=$JAVA_HOME/bin:$MAVEN_HOME/bin:$PATH

source /etc/profile
```

### CAS安装
为了节约时间，减少安装编译过程，使用gradle overlay项目。

```
# 下载到指定文件夹
cd /app && git clone https://github.com/apereo/cas-gradle-overlay-template.git

# 进行编译
cd cas-gradle-overlay-template && ./build.sh package # 需要很长时间

# 编译完成后会在/app/cas-gradle-overlay-template/cas/build/libs目录中生成cas.war文件，将此文件复制到webapps文件中
cp /app/cas-gradle-overlay-template/cas/build/libs/cas.war /app/apache-tomcat-8.5.35/webapps/

# 重启tomcat
```

![cas-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-2.png)

## 配置
