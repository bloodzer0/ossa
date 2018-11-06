## 概述
对于互联网公司来说，企业服务器很多时候变化很频繁，所以我们需要做到自动化来部署agent，同样在服务器量级很大的时候我们也需要一键部署。


对于agent比较重要的就是服务端先生成密钥，然后客户端获取密钥导入文件中。

[参考地址](https://documentation.wazuh.com/current/user-manual/agents/restful-api/register.html)

## 自动化实现
[代码地址](https://raw.githubusercontent.com/wazuh/wazuh-api/3.5/examples/api-register-agent.sh)

大家可以根据需求来进行修改，修改完成以后就可以与运维协调在系统初始化的时候加入客户端自动部署的代码。
