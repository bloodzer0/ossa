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

![cas-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-1.png)

* 配置环境变量

```
vim /etc/profile
	export JAVA_HOME=/app/jdk1.8.0_191
	export MAVEN_HOME=/app/apache-maven-3.6.0
	export PATH=$JAVA_HOME/bin:$MAVEN_HOME/bin:$PATH

source /etc/profile
```

### CAS安装

* **Gradle编译安装（不推荐）**

为了节约时间，减少安装编译过程，使用[gradle overlay](https://github.com/apereo/cas-gradle-overlay-template)项目。

```
# 下载到指定文件夹
cd /app && git clone https://github.com/apereo/cas-gradle-overlay-template.git

# 进行编译
cd cas-gradle-overlay-template && ./build.sh package # 需要很长时间
# 或者使用
cd cas-gradle-overlay-template && ./gradlew clean build

# 编译完成后会在/app/cas-gradle-overlay-template/cas/build/libs目录中生成cas.war文件，将此文件复制到webapps文件中
cp /app/cas-gradle-overlay-template/cas/build/libs/cas.war /app/apache-tomcat-8.5.35/webapps/

# 重启tomcat
```

![cas-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-2.png)

默认账号密码：casuser/Mellon，登录进入系统，但此账号并不具备任何功能

![cas-3](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-3.png)

* **Maven编译安装（推荐）**

```
# 下载到指定文件夹：Branch版本选择5.3即可下载Maven版本
# 直接下载zip文件，不要git clone 否则会下载的还是最新版本
```

![cas-4](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-4.png)

```
# 编译安装
mvn install # 漫长的过程

# 编译完成后与Gradle后续步骤一致
# 生成的文件在target目录中
```

### 配置SSL证书
* **配置证书**

```
# 生成server.keystore
keytool -genkey -alias tomcat -keyalg RSA -keypass tomcat -storepass tomcat -keystore server.keystore -validity 3600

# alias 别名
# keyalg 证书算法
# keystore 证书生成的目标路径和文件名
# keypass 密钥保护密码
# storepass 存储密码
```

![cas-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-5.png)

```
# 生成证书server.cer
keytool -export -trustcacerts -alias tomcat -file server.cer -keystore server.keystore -storepass tomcat
```

![cas-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-6.png)

```
# 导入证书
keytool -import -trustcacerts -alias tomcat -keystore "/app/jdk1.8.0_191/jre/lib/security/cacerts" -file "/root/server.cer" -storepass changeit

# 备注：证书库默认密码changeit
```

![cas-7](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-7.png)

```
# 删除证书
keytool -delete -alias tomcat -keystore "/app/jdk1.8.0_191/jre/lib/security/cacerts" -storepass changeit
```

* **tomcat配置https**

```
# 移动证书文件位置
mv server.keystore /app/
mv server.cer /app/

# 修改server.xml文件
vim /app/apache-tomcat-8.5.35/conf/server.xml

# 在对应位置添加如下内容
<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol" maxThreads="150" SSLEnabled="true" scheme="https" secure="true" clientAuth="false" sslProtocol="TLS" keystoreFile="/app/server.keystore" keystorePass="tomcat" />

# 重启tomcat
```

![cas-8](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Infrastructure%20Security/Identity%20Access%20Security/img/cas-8.png)

## 高级使用
### 配置数据库

## 参考资料
[CAS统一登录认证(1)：系统安装](https://blog.csdn.net/oLinBSoft/article/details/81910775)
