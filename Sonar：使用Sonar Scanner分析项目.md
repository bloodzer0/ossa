# 安装
[下载地址](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner)

```
# 解压
unzip sonar-scanner-cli-3.2.0.1227-linux.zip

# 添加环境变量
vim /etc/profile

export SONAR_SCANNER_HOME=/opt/sonar-scanner-3.2.0.1227-linux
export PATH=$PATH:$SONAR_SCANNER_HOME/bin

source /etc/profile

# 验证是否安装成功
sonar-scanner -v
```

![sonar-5](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Application%20Security/Code%20Audit/img/sonar-5.png)

# 分析
[官方文档](https://docs.sonarqube.org/display/SCAN)
## JS、Python、PHP等
在项目目录执行

```
sonar-scanner \
  -Dsonar.projectKey=first \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://192.168.199.5:9000 \
  -Dsonar.login=token
```

## 其他语言
### Java-Maven
```
mvn sonar:sonar \
  -Dsonar.host.url=http://192.168.199.5:9000 \
  -Dsonar.login=token
```

### Java-Gradle
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

### C# 与 VB.NET
```
SonarScanner.MSBuild.exe begin /k:"test" /d:sonar.host.url="http://192.168.199.5:9000" /d:sonar.login="token"

MsBuild.exe /t:Rebuild

SonarScanner.MSBuild.exe end /d:sonar.login="token"
```

## 使用配置文件
在项目目录新建sonar-project.properties文件，文件内容如下：

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

# 测试
我这里使用sqlmap的代码进行测试，执行完成以后，在web界面可以看到如图分析结果

![sonar-6](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Application%20Security/Code%20Audit/img/sonar-6.png)
