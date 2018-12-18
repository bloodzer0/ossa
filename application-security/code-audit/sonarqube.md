# SonarQube
开源代码质量管理工具。

[官网地址](https://www.sonarqube.org/)

[官方文档](https://docs.sonarqube.org/display/SONAR/Documentation/)

## SonarQube安装与使用
### Centos7安装SonarQube
> 数据库不要使用mariadb
> 
> 不要使用root进行安装，es会提示"can not run elasticsearch as root"

* **解决依赖**

```
# 安装jvm环境
yum install java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64 -y

# 安装数据库
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

rpm -ivh mysql57-community-release-el7-10.noarch.rpm

yum install mysql-community-server.x86_64 -y

# 配置数据库
systemctl start mysqld.service

# 数据库初始密码
grep "password" /var/log/mysqld.log

# 修改数据库密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';

# 创建数据库sonar
create database sonar;
```

* **安装SonarQube**

```
# 下载，从这里开始应该使用普通用户操作
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.3.zip

# 解压
unzip sonarqube-7.3.zip

# 启动sonarqube
cd sonarqube-7.3/
./bin/linux-x86-64/sonar.sh start

# 访问：http://10.10.10.5:9000/sessions/new
# 账号：admin 密码：admin
```

![sonarqube-1](https://github.com/bloodzer0/ossa/raw/master/application-security/code-audit/img/sonar-1.png)

### 配置SonarQube
* **配置数据库**

```
vim ./conf/sonar.properties
# 注释如下三行，并添加数据库账号密码
```

```
sonar.jdbc.username=root
sonar.jdbc.password=password

sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false
```

![sonarqube-2](https://github.com/bloodzer0/ossa/raw/master/application-security/code-audit/img/sonar-2.png)

```
# 重启服务
./bin/linux-x86-64/sonar.sh restart
```

* **汉化**

在Administration菜单栏，选择Marketplace标题，在搜索框输入chinese进行搜索，然后进行安装

![sonar-3](https://github.com/bloodzer0/ossa/raw/master/application-security/code-audit/img/sonar-3.png)

安装完成后重启

![sonar-4](https://github.com/bloodzer0/ossa/raw/master/application-security/code-audit/img/sonar-4.png)

## SonarQube Scanner
### SonarQube Scanner安装
```
# 下载
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-3.2.0.1227-linux.zip

# 解压
unzip sonar-scanner-cli-3.2.0.1227-linux.zip

# 添加环境变量
cd sonar-scanner-3.2.0.1227-linux/
vim /etc/profile
```

```
export SONAR_SCANNER_HOME=/home/bloodzer0/sonar-scanner-3.2.0.1227-linux
export PATH=$PATH:$SONAR_SCANNER_HOME/bin
```

```
source /etc/profile

# 验证安装
sonar-scanner -v
```

![sonar-5](https://github.com/bloodzer0/ossa/raw/master/application-security/code-audit/img/sonar-5.png)

### SonarQube Scanner执行分析
token在Web页面生成，点击项目--分析新项目。

![sonar-6](https://github.com/bloodzer0/ossa/raw/master/application-security/code-audit/img/sonar-6.png)

* **JS、Python、PHP等语言**

```
sonar-scanner \
  -Dsonar.projectKey=security-test \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://10.10.10.5:9000 \
  -Dsonar.login=token
```

* **测试分析sqlmap代码**

```
git clone https://github.com/sqlmapproject/sqlmap.git
cd sqlmap

# 执行上面的代码
# 分析完成后，在web界面查看分析结果
```

![sonar-7](https://github.com/bloodzer0/ossa/raw/master/application-security/code-audit/img/sonar-7.png)

* **Java-Maven**

```
mvn sonar:sonar \
  -Dsonar.host.url=http://10.10.10.5:9000 \
  -Dsonar.login=token
```

* **Java-Gradle**

```
# 在build.gradle文件中声明

plugins {
  id "org.sonarqube" version "2.6"
}

# 然后执行命令
./gradlew sonarqube \
  -Dsonar.host.url=http://192.168.199.5:9000 \
  -Dsonar.login=token
```

* **C# 或 VB.NET**

```
SonarScanner.MSBuild.exe begin /k:"test" /d:sonar.host.url="http://192.168.199.5:9000" /d:sonar.login="token"

MsBuild.exe /t:Rebuild

SonarScanner.MSBuild.exe end /d:sonar.login="token"
```

* **使用配置文件进行扫描**

在项目目录新建sonar-project.properties文件

```
# must be unique in a given SonarQube instance
sonar.projectKey=my:project
# this is the name and version displayed in the SonarQube UI. Was mandatory prior to SonarQube 6.1.
sonar.projectName=My project
sonar.projectVersion=1.0
 
# Path is relative to the sonar-project.properties file. Replace "\" by "/" on Windows.
# This property is optional if sonar.modules is set. 
sonar.sources=.
 
# Encoding of the source code. Default is default system encoding
#sonar.sourceEncoding=UTF-8
```
