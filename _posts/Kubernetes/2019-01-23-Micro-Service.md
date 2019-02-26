---
layout: post
title: 宝宝架构路(一) —— 分布式链路追踪与服务注册发现调研
categories: Infrastructure Architecture
description: 在微服务过程中，对于分布式全链路追踪、服务注册和发现的一点感悟。
keywords: Infrastructure Microservice
---

# 宝宝架构路(一)

## 背景

**微服务**，核心概念就是组件化思想，通过用小的组件化服务，以轻耦合的模式去构建大型的网络应用。

> 借鉴[网易云<sup>[0]</sup>](https://www.163yun.com/product-nsf?tag=M_zhihu_65502802_jd)的观点，一个完整微服务体系应该包括**API网关**(流量分发、降级、熔断)、**容器化**（支持弹性横向扩容）、**网络规划和治理**(服务注册、发现、鉴权、网络拓扑)、**DevOps**(开发工具链: 代码、发布、构建镜像)、**分布式链路追踪**(全链路、报警、监控、故障定位分析) 和 **自动化测试**(API测试、压测)。

我觉得这个描述还是很全面化的，对于将团队业务或是自己的个人项目进行微服务应用，也应该是，在一个合适的阶段，从上面这些方面，从于合理的切入点，进行微服务改造，而不应该为了微服务而微服务。



## 关于分布式链路追踪的一点实践经验

就本宝宝来说，在我毕业入职之后，工作主要是对所在团队（核心风控）进行基础架构方面的优化升级。我自己参与了的，是分布式链路追踪这一环的开发和实现。基于团队的主要技术栈是Python(服务框架 Flask + Celery)，我们的解决方案是基于Google的一篇分布式追踪论文[Dapper<sup>[1]</sup>](https://bigbully.github.io/Dapper-translation/)来实现全链路的嵌入。Google对这篇论文有一个相应的开源工具，[Zipkin<sup>[2]</sup>](https://zipkin.io/)，我们自己对其进行改造，替换了封装框架、日志解决方案等，保留了context的机制。从而使得每次只需要对统一底层包的迭代更新，就可以对所有线上服务进行更新。但是弊端是，我们把链路代码嵌入了业务代码。

在改造过程中，我们也研究了[美团的全链路解决方案<sup>[3]</sup>](https://tech.meituan.com/2018/01/15/satellite-system.html)，这种方案不需要把链路代码嵌入业务代码，而是在日志层面进行链路收集，但相应的，对日志格式化和规范化也提出了很高的要求。

这里有一个各个解决方案的对比：[掘金-几种分布式调用链监控组件的实践与比较（一）实践<sup>[4]</sup>](https://juejin.im/post/5a0579e6f265da4326524f0f)。



## 关于服务注册与发现的一点调研

宝宝在团队Kubernetes容器集群化的道路上步履维艰，不过也好，对于团队来说是换技术债，对于宝宝来说是补技术栈。。。

如果没有合理的网络规划，在从传统服务器向K8S集群迁移的时候，最大的困难一定是ingress。所以宝宝才要来折腾一下服务注册与发现。

服务注册和发现，其实不是新话题了，当服务数量始终处于相对固定时，可以通过手动配置静态配置文件，维护Nginx的形式进行服务间的调用和通信。但是，在微服务中，弹性扩容已经是必不可少，微服务实例的网络位置发生变化是一种常态。在这个过程中，如果还要维护映射配置文件，对于运维来说就是灾难。所以，我们需要服务注册和发现机制。

通常的服务注册和发现分两种模式：

**客服端发现模式**

![img](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1548323752/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7_2019-01-24_%E4%B8%8B%E5%8D%885.47.47.png)

较少的网络跳转，但要嵌入业务代码。



**服务端发现模式**

![img](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1548323752/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7_2019-01-24_%E4%B8%8B%E5%8D%885.50.54.png)

不需要嵌入业务代码，集中在中央路由器或者负载均衡器做处理，但是需要对其搭配较高的基础设施环境。

目前比较流行的服务注册与发布解决方案，包括诸如 ZooKeeper、Etcd等的利用健值对存储建立服务发现系统，和Netflix的开源项目Eureka，以及HashiCorp公司的Consul。

关于选型，网上也有很多对比图，其中最重要的区别可能就是对[CAP(Consistency、Availablility、Partition tolerance)<sup>[5]</sup>](https://zh.wikipedia.org/wiki/CAP%E5%AE%9A%E7%90%86)定理的选择了。具体的感悟，宝宝正在实践，等到有所产出和成果，再来写吧。

## Next?

应该是服务注册与发现的实践体悟了吧，祝自己成功……



## 参考链接

* [网易云微服务设计理念<sup>[0]</sup>](https://www.163yun.com/product-nsf?tag=M_zhihu_65502802_jd)
* [Google的分布式追踪解决方案<sup>[1]</sup>](https://bigbully.github.io/Dapper-translation/)

* [美团的全链路解决方案<sup>[3]</sup>](https://tech.meituan.com/2018/01/15/satellite-system.html)
* [掘金-几种分布式调用链监控组件的实践与比较（一）实践<sup>[4]</sup>](https://juejin.im/post/5a0579e6f265da4326524f0f)

* [CAP - 布鲁尔定理 <sup>[5]</sup>](https://zh.wikipedia.org/wiki/CAP%E5%AE%9A%E7%90%86)