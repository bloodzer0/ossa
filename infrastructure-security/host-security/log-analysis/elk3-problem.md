# ELK问题踩坑

## ES脑裂
### 脑裂可能的原因

* 网络问题：集群间的网络延迟导致一些节点访问不到master，认为master挂掉了从而选举出新的master，并对master上的分片和副本标红，分配新的主分片。
* 节点负载：主节点的角色既为master又为data，访问量较大时可能会导致ES停止响应造成大面积延迟，此时其他节点得不到主节点的响应认为主节点挂掉了，会重新选取主节点。
* 内存回收：data节点上的ES进程占用的内存较大，引发JVM的大规模内存回收，造成ES进程失去响应。

### 解决脑裂
* 减少误判：discovery.zen.ping_timeout节点状态的响应时间，默认为3s，可以适当调大，如果master在该响应时间的范围内没有做出响应应答，会判断该节点已经挂掉了。（调整参数为：6s，discovery.zen.ping_timeout:6，可适当减少误判。）
* 选举触发：discovery.zen.minimum_master_nodes:1，当备选主节点的个数大于等于该参数的值，且备选主节点中有该参数个节点认为主节点挂了，进行选举。官方建议为(N/2)+1，N为主节点个数（即有资格成为主节点的节点个数）
* 角色分离：即master节点与data节点分离，限制角色

```
# 主节点
node.master: true
node.data: false

# 从节点
node.master: false
node.data: true
```

## 参考资料
[ES脑裂问题分析及优化](https://blog.csdn.net/kakaluoteyy/article/details/81068387)
