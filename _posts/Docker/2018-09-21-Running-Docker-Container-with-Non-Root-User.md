---
layout: post
title: 在Dockerfile中以non-root身份运行Container
categories: Docker
description: 本文描述如何在Dockerfile中以显示方式指定普通用户身份运行Container
keywords: Docker Dockerfile Container 
---

# 在Dockerfile中以non-root身份运行Container

## Introduction

> 本文假设读者已有一定的Docker相关基本概念的知识。否则，请先参阅官方文档。

在Dockerfile中，如果我们不显示的表明用户，进行权限处理，那么默认的Dockerfile生成的镜像，在运行时会以`root`身份进入`ENTRYPOINT`，进而执行`CMD` 。然而，以`root`身份启动container是一件很危险的事情。尽管，docker容器内的`root`与宿主host本身的`root`并不一定具有一样的权限，但是如果已priviledge的方式运行container，那么两者将会一样，从而产生巨大的安全隐患。



因此，在Dockerfile的`ENTRYPOINT`之前，也就是运行application之前，进行用户切换。本文就如何在Dockerfile中创建普通用户，附属的`pip`、`git`、`ssh`中可能遇到的问题，以及给出Best Practice的demo。



## How To

**Basic**

首先看一个最简单的Dockerfile

![Basic Dockerfile](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537503785/For%20Blog/Dockerfile.png)

在这个例子里，该Dockerfile使用了官方Docker Hub中的Python3.6镜像，把当前上下文`context`的项目代码拷贝到目标镜像的`/code`下，并把这个目录作为工作目录的上下文基址。

这个例子中，并没有显示的声明用户，所以最后是默认以`root`身份运行的。



**Create a User**

放一个例子，

![create a user](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537504002/For%20Blog/Dockerfile_create_user.png)

这个例子展示了如何创建普通用户app，并在`ENTRYPOINT`之前切换到app用户。

这里需要注意的问题是：

1. **Python虚拟环境**：我推荐的做法是： 秉承一个项目一个解释环境的原则。以一个Web应用来说，通常包含不止一个container，App，db等分别有各自的container，所以，单独的运行项目的容器内，可以直接使用Python环境进行pip install操作
2. **如果你使用私有Docker Hub**：如果你要使用公司自有的Docker Hub，最好的操作是在基础镜像中，就配置好`Python`、`Git`和`ssh`，避免在对项目进行容器化封装时候，手动从本地封装进去这些配置文件。
3. **关于新用户目录**： Linux中新建用户其实默认会帮用户在`/home/`下新建一个用户目录，并分配所属和权限。在Dockerfile中，我们一般通过环境变量`ENV`的方式来操作这个上下文目录。但是，环境变量名称请勿使用保留字，比如`ENV HOME=/home/app`，这是因为这个`$HOME`会覆盖掉基础镜像本身的home，从而引起各种权限配置文件丢失的问题。



**Running on Server with Gunicorn**

上面的例子使用`Python`跑起来的，但是当运行在服务器上时，我们应该以`Gunicorn`启动。

![Gunicorn](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537505153/For%20Blog/Dockerfile_gunicorn.png)


## 参考链接

* [Docker从入门到实践](https://yeasy.gitbooks.io/docker_practice/image/dockerfile/user.html)
* [Create a User in Dockerfile]\(https://gist.github.com/alkrauss48/2dd9f9d84ed6ebff9240ccfa49a80662)

