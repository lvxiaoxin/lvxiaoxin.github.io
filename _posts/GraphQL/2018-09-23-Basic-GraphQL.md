# 浅谈GraphQL

最近在用`GitHub`的API写一个小工具，发现`GitHub`和`GitLab`最新版的API全部迁移向了GraphQL的实现模式。因为以前没有接触过这个概念（好吧，这应该是我这个刚毕业的小萌新孤陋寡闻了），所以想着写个demo了解一下。



本文将主要从**What is GraphQL**，**GraphQL vs REST API**，**Why GraphQL** 以及一个小**demo**，来简要介绍GraphQL。



## What is GraphQL

在我这个小萌新看来，**GraphQL**其实是一门语言，它是一种**强类型(schema)**，**声明式(Declarative)**，用于客户端和服务端进行通信的语言。它独立于客户端和服务端各自的实现，可以帮助客户端向服务端描述自己想要的数据。



没错，它想做和REST API一样的事情。



## What's Wrong with REST API

我们在工业级生产中，已经有了一套很成熟的基于REST的应用体系。所谓REST，即一切即资源。任何一个我们日常访问的URL，作为一个**端点(endpoint)**，都代表了一种资源。这种设计模式下，可能存在的问题有：

* **1+n问题**
  * REST下的请求，无非：
    * GET /Resource —> 返回资源列表
    * GET /Resource/id —> 返回单个资源
  * 因为一个端点只代表一种资源，因此，单次请求可能无法满足客户端所要请求数据的要求，从而需要多伦请求。
  * 比如：客户端有某一用户的customer_id，想要拿到改用户第一联系人(contact_a)的姓名。那么，第一次请求，服务端返回了此用户第一联系人的customer_id，第二次请求，服务端才能根据这个第一联系人的customer_id，拿到其姓名。
* **过度加载(Overfetching)**
  * 客户端没有描述自己想要哪些数据的能力 - 通常来说，针对特定端点的返回是固定的，即使客户端只想要这个端点资源中的某一个字段，服务器也会返回整个资源给它。
* **版本演化(Version Evoluation)**
  * 对于REST来说，同一个**端点(endpoint)**下，版本没有兼容一说。
  * 解决这一问题的手段，REST是通过增加新的端点来实现，从而满足客户端新的需求，同时保留老的端点，以满足老版的客户端的需求。从而导致，服务端可能存在大量重复代码。



## Why GraphQL 

不得不说，GraphQL刚好在上面所描述的REST存在的几个问题方面，提出了较好的解决方案。

* **强schema** + **单端点**
  * GraphQL 模式是强类型的模式，通过用scheme type这种形式来定义各个字段的数据形式和关系（relationship)，就可以让客户端以一种相对灵活的方式来请求服务端。
  * GraphQL对外只暴露一个接口，**GET or POST - /graphql?query={…}**，通过GraphQL这个声明式的语言，客户端可以一次性的描述清楚，自己要哪些数据，要多少数据。
  * 即赋予了客户端描述数据的能力。
* **避免过度加载**
  * 服务端可以根据最初schema定义的关系，针对客户端描述的数据，有选择的返回数据。
* **以图的模式进行版本演化**
  * 新版本的演化，以在schema定义的图结构上增加新的节点的方式来完成，从而向前兼容，同时不必增加新的端点接口。



## Demo

> Demo 的完整代码，可在 https://github.com/lvxiaoxin/A-Simple-API-with-GraphQL 处找到，此处仅进行必要的描述。

GraphQL模式是一种思想，所以它并不局限于某一种特定的语言。很多种语言都有实现好的Library来帮助我们实现GraphQL服务器。这里，我用**Graphene**(library for building GraphQL APIs in Python)来使用Python制作一个简易的GraphQL API。我的后端框架选择**Flask**，数据库选择**SQLite**，嗯，都是最轻量级的。



我打算制作一个API，这个API提供查询《权力的游戏》中，某一任务所属家族的城堡名字，这样一个借口功能。可以想象，如果使用REST API，在不在后端实现复杂数据库查询逻辑的前提下，我们需要不止一次请求才能实现。



首先，我希望我的后端入口程序长这个样子：

![server](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537693461/For%20Blog/Graphql-server.png)

这样的入口表明，我的程序永远只有 **/graphql** 这一个接口。



我们的数据库，将维护两张表，一张Members表，一张Families表：

![db](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537693666/For%20Blog/gql-db.png)



接下来，就是重点的：定义schema。

因为我这里使用的是基于Python实现的library，所以将图结构化的表述（其实就是JSON），进行了代码化的描述：

![schema](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537693843/For%20Blog/gql-schema.png)

在上面这份代码中，我定义了`Families`和`Members`这两个基础数据类型，同时，通过**relay.Node**将两者进行了关联。



在**MyMutation**中，我定义了新建立两个数据类型实例的接口，在**Query**中，我实现了查询全部Members、查询全部Families、查询特定Member、Family等的接口。



下面是测试样例：

![input](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537694343/For%20Blog/graphql-client.png)



这就是**GraphQL**声明式的实现。

可以看到，我们要查询的，是名字叫**"Tyrion Lannister"**（小恶魔）的，家族的城堡名字。



下面是返回结果：

![res](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537694444/For%20Blog/gql-res.png)

有没有发现，GraphQL，其实就是把客户端的请求，当做一个JSON，或者Dict，然后把对应Key的Value给填进去。真正做到了，要什么，给什么。



最后再来看一下网络请求次数：

![net](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537694626/For%20Blog/graphql-net.png)



只有一次网络请求产生。



更多的请求样例，参见我的GitHub。



## Ending

但是不得不说，把GraphQL全面应用到企业级，还有很长的路要走。毕竟，相比于这个，还有很多其他的事情要做。比如，迁移Kubernetes... ... 