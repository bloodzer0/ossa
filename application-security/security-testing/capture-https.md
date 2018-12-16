## 概述
### 常用抓包工具
* burpsuite
* fiddler
* charles

## 如何抓取https的包
### 接受所有证书的app
不对证书做严重，直接通过代理就可以抓包；

### 使用系统证书进行校验的app
* 将代理工具的证书导出为cer格式；
* 将导出的证书安装到手机或浏览器中；

### 绕过证书的SSL Pinning
* **xposed + Just Trust Me**

备注：mac电脑升级到Majave版本后，夜神模拟器有bug，推荐使用mumu或者Genymotion模拟器。

这里说一下mumu模拟器安装Xposed+Just Trust Me的过程：

[Xposed下载地址](http://repo.xposed.info/module/de.robv.android.xposed.installer)

[Just Trust Me下载地址](https://github.com/Fuzion24/JustTrustMe/releases/)

安装最新版的xposed框架会报错如下：

![xposed-1](https://github.com/bloodzer0/ossa/raw/master/application-security/security-testing/img/xposed-1.png)

需要使用如下版本的xposed：[传送门](https://pan.baidu.com/s/11PncIOtrEY7vthB9CpgO2Q)

备注：mumu模拟器需要在设置--应用兼容性--关闭

安装完成如图所示：

![xposed-2](https://github.com/bloodzer0/ossa/raw/master/application-security/security-testing/img/xposed-2.png)

* **frida绕过**

### IOS绕过SSL Pinning
手机越狱后，使用SSL Kill Switch2

[ssl-kill-switch2下载地址](https://github.com/nabla-c0d3/ssl-kill-switch2)

## 参考资料
[突破https-https抓包](https://blog.csdn.net/justfwd/article/details/78767328)
