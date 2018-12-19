# GitHub信息泄漏监控
为什么要监控GitHub，因为随着企业发展，各个团队信息安全意识不到位，可能导致涉及公司的敏感信息被上传到GitHub，主要包括：公司业务代码信息、服务器账号密码或私钥信息、内部核心资料（员工信息、项目文档）等等。

GitHub监控只是一个辅助手段，不要过分依赖监控平台，因为GitHub监控需要人员参与处理，所以会存在漏报的情况。**我们需要做到从制度管控到员工安全意识培训等多方面来提高整体安全水平。**

## 监控模式
### 通过API进行监控
token有查询频次限制。

[API文档](https://developer.github.com/v3/search/#search-code)

### 通过爬虫进行监控
GitHub存在反爬机制，针对频次太快的IP与UA维度（这个是很早之前测试发现的，现在的策略可能发生了变化）。

应对反爬机制：

```
降低请求的时间间隔：5s/请求基本就不会被拉黑；
使用IP代理池，需要考虑代理池的稳定性与代理池IP能在被拉黑后快速切换；
```

## GitHub监控规则
### 关键字匹配模式
* 精准匹配："keyword"，使用双引号把关键字引起来；
* 模糊匹配：keyword，常用于多个关键字联合搜索，如：domain password;

备注：模糊匹配经常会出现匹配出海量结果的情况；

### 优化匹配规则
* 是否应该选择排除不属于公司的开发语言：否，研发可能自己使用其它语言编写代码；
* 文件后缀名排除：排除gif、png、jpg等类似的后缀名文件；
* commit内容爬取：调用API进行监控就可以获取信息；

### 加白操作
* 加白某个用户：比如一些白帽子的项目中会有很多关于公司的关键字，可以考虑加白这个人；
* 加白项目：加白项目的时候需要考虑如果项目被fork以后，我们如果标记那些被fork的项目；

### 前端展示
我思考的最优方案：通过项目来展示，一个项目中有多少可疑的URL，每个URL展示命中关键字的部分，这样方便运营人员在处理是进行加白。

## 几个思考
### 确认的GitHub应该如何处理
* GitHub可以追溯到人，直接联系到人进行删除；
* 不能追溯到人，GitHub支持给官网发送邮件，联系GitHub进行删除；但是GitHub会公开他们处理的结果，所以如果真的由于不能追溯到人泄漏，赶紧想办法做其它处理吧；如：泄漏代码，通过能接触到这些代码的人进行查询；泄漏的是账号密码，可以反查能接触账号密码的人，也可以直接修改密码；（主要还是需要从制度与安全意识出发）

[GitHub链接](https://help.github.com/articles/dmca-takedown-policy)

[GitHub公布处理结果](https://github.com/github/dmca)

### 如何构建分布式监控

### 科技型企业支撑集团如何考虑不同公司的用户权限控制

## 开源GitHub监控
* [gsil](https://github.com/FeeiCN/GSIL) 美联开源
* [x-patrol](https://github.com/MiSecurity/x-patrol) 小米开源
* [Hawkeye](https://github.com/0xbug/Hawkeye)
* [Github-Monitor](https://github.com/VKSRC/Github-Monitor) VIPKID SRC开源

### 开源优缺点对比
开源的产品都是基于两种监控模式重复造的轮子，每个人都有自己的习惯，所有对于每个人优缺点都不一样；

对比项		| gsil			| x-patrol		| GitHub-Monitr
---			| ---			| ---				| ---
监控模式	| API模式		| API模式			| API模式
开发语言	| python		| go				| python
docker支持	| 不支持		| 不支持			| 支持
