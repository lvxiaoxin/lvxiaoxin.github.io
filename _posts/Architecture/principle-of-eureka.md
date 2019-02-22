# Eureka 基础原理

## Eureka基本架构

**原理架构**

![image-20190221103629479](/Users/xinlyu/Library/Application Support/typora-user-images/image-20190221103629479.png)

**集群架构**

![image-20190221103822204](/Users/xinlyu/Library/Application Support/typora-user-images/image-20190221103822204.png)

**Eureka Server**：注册中心，高可用，协同信息，管理注册表

**Service Provider**：向Eureka Server注册自身(Register)，续约(Renew，保持心跳)，或主动请求剔除(Cancel)

**Service Consumer**：向Eureka Server注册自身，获取调用对象信息(Fetch Registrier)，调用(Remote call)



## 对于Python的实践





## 对比ZooKeeper

**Eureka**：

**高可用性**：支持嵌入到自身的容器中，多个Eureka Server相互注册，达到高可用性。

**Eureka关于宕机的处理**：切换到新的节点，当宕机的节点服务恢复后，再纳入管理范围。



**ZooKeeper**：ZooKeeper是分布式协同服务，满足CP原则。

在分布式系统领域有个著名的 [CAP定理](http://zh.wikipedia.org/wiki/CAP%E5%AE%9A%E7%90%86)（C- 数据一致性；A-服务可用性；P-服务对网络分区故障的容错性，这三个特性在任何分布式系统中不能同时满足，最多同时满足两个）。

**数据一致性算法：**Raft 协议强依赖 Leader 节点的可用性来确保集群数据的一致性。数据的流向只能从 Leader 节点向 Follower 节点转移。

![image-20190221104927627](/Users/xinlyu/Library/Application Support/typora-user-images/image-20190221104927627.png)