---
layout: post
title: Scope in Python
categories: Python
description: Python中作用域的那些事儿
keywords: Python
---

#  Scope in Python

本系列文章着重描述`Python`语言中一些较为重要的话题，作为学习和记录。

> 献给我的老婆——娴。


## 作用域 命名空间 约束

不仅是Python，在各种编程语言中，都逃不开作用域、命名空间，约束这些概念和话题。 每个命名空间，都对应一个代码片段作用域。而每个命名空间内，都可以有很多约束，每个约束一旦建立，就将持续影响后续代码的执行，直至超出本作用域/命名空间的范围。

Python的LEGB原则

在`Python`中，变量的查询是按照LEGB原则进行的：

* local: 局部变量 + 形参
* enclosing functions: 外层函数变量，闭包
* global：全局变量，函数定义所在的模块
* built-in: 内置模块的命名空间

变量查找的顺序是`L --> E --> G --> B`。

具体实践可以看一段代码：

![exp](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537855710/For%20Blog/legb.png)



这段代码的输出结果为：

```python
All globals: {'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x104e325c0>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, '__file__': 'legb.py', '__cached__': None, 'fun': <function fun at 0x104c10e18>, 'a': 1}
All locals {}
global -> 1
local -> 2
<built-in function open>
enclosing functions -> 2
```

