## APP上线安全流程
参考阿里移动安全技术实践

* 建项

```
审核：业务功能、发版计划、基线要求
```

* 开发

```
评审/审计：协议、数据、框架、逻辑
```

* 集成/测试（CI）

```
工程质量卡口：安全编码规范、持续白盒扫描、自动化黑盒扫描、业务自测、人工审计
```

* 发布

```
上线：API权限审核、防刷监控、漏洞/事件/补丁
```


## 移动安全工具
### 静态工具
* Apktool：apk反编译工具
* Dex2jar：dex反编译jar工具
* Jd-gui：jar反编译java工具
* ZjDroid：脱壳工具

### 动态分析工具
* ADB：Android debug工具
* IDA：反汇编软件
* Drozer：安全测试框架
* IntentFuzzer：Intent Fuzz测试工具
* Xposed：Android Hook框架
