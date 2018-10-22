谈到MobSF，大家最直观的看法可能就是针对Android/IOS/Windows自动测试框架，能够进行静态与动态分析。

在乙方，更多的安全工程师把MobSF作为一个安全测试工具来挖掘移动端的漏洞，在甲方也更多的用于安全测试工作。现在网络上公开的关于MobSF相关文档也主要是安装与基础使用，并没有很好的形成一个流程。

**为什么MobSF没有在甲方推动起来：测试发现的很多漏洞业务都会忽略。**

那么如何把MobSF作为甲方移动安全流程中的一个关键点，首先我们要思考几个问题：

* MobSF系统运行的稳定性；
* 如何进行自动化扫描；
* 扫描结果存在的漏报、误报；或者是系统认为存在问题，但是实际在业务中任务不存在安全隐患；
* 如何对第三点描述的问题进行处理；
* 处理后的结果如何自动推送给相应的业务/研发；

后面我们就根据这几点的问题来思考解决方案（仅限于静态分析部分）

## 保障MobSF运行的稳定性
MobSF运行分为两种类型：Docker运行、安装在服务器/PC上；

推荐使用Docker：部署方便

## 自动化扫描
利用MobSF API进行自动化扫描：[API文档](https://github.com/MobSF/Mobile-Security-Framework-MobSF/wiki/3.-REST-API-Documentation)

或者使用MobSF CI项目进行：[MobSF-CI](https://github.com/Soluto/mobsf-ci)

关于自动化扫描应该在什么位置介入，冰粥大佬在文章中提到过：

[原文地址](https://mp.weixin.qq.com/s/5whLNkfSUpW6zQNxQf7HqQ)


![MobSF-1](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Application%20Security/Mobile%20Security/img/MobSF-1.png)


## 排除误报项与易忽略项
目前针对MobSF扫描出来的漏洞，我们最好的办法就是将JSON格式的report通过正则进行二次处理，排除我们不想进行预警的选项。

## 报警与漏洞管理
对处理后的报告重新进行下一步：预警和漏洞管理并行进行；

![MobSF-2](https://github.com/bloodzer0/Enterprise_Security_Build--Open_Source/blob/master/Application%20Security/Mobile%20Security/img/MobSF-2.png)

## 总结
代码审计与扫描都是一个需要人来进行运营的东西，如果在没有精力能够投入这个版块时，安安心心把MobSF作为一个漏洞挖掘工具就好了。
