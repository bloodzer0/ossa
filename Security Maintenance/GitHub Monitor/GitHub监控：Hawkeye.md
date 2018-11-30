[Hawkeye](https://github.com/0xbug/Hawkeye)

## 简介

监控github代码库，及时发现员工托管公司代码到GitHub行为并预警，降低代码泄露风险。

2018-10-12更新后支持Docker部署

## 安装

```
# 这里我是手动build的

git clone https://github.com/0xbug/Hawkeye.git --depth 1
cd Hawkeye
docker build -t hawkeye .
docker run -ti -p 80:80 -e MONGODB_URI=mongodb://username:password@ip:27017/hawkeye -e MONGODB_USER= -e MONGODB_PASSWORD= -d hawkeye ## mongodb 需认证
docker run -ti -p 80:80 -e MONGODB_URI=mongodb://ip:27017 -d hawkeye ## mongodb 无认证
```