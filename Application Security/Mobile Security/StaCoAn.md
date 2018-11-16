[github地址](https://github.com/vincentcox/StaCoAn/)

[下载地址](https://github.com/vincentcox/StaCoAn/releases/download/v0.90/linux.zip)

## 安装
操作系统：Centos7

```
# 解决依赖
yum install java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64 -y

# 下载
wget https://github.com/vincentcox/StaCoAn/releases/download/v0.90/linux.zip

unzip linux.zip && cd deploy

# 启动服务
./stacoan

# 访问
server:7777
```

## 使用
上传apk文件进行分析，会生成报告

![StaCoAn-1.png](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/raw/master/Application%20Security/Mobile%20Security/img/StaCoAn-1.png)
