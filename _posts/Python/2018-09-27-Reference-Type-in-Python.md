---
layout: post
title: 从动态类型语言，到Python的Objective Reference
categories: Python
description: 前几天看到一篇讨论编程范式，写变量传递的文章，发现自己对Python的相关机制理解还不够深入，就查阅了一些资料，做了一些实验，请教了一下同事。最近老婆大人也在学习Python，这里做下记录，仅供总结和学习~
keywords: Python Objective-Reference Reference
---

# 从动态类型语言，到Python的Objective Reference

本系列文章着重描述`Python`语言中一些较为重要的话题，作为学习和记录。



> 备注：前几天看到一篇讨论编程范式，写变量传递的文章，发现自己对Python的相关机制理解还不够深入，就查阅了一些资料，做了一些实验，请教了一下同事。最近老婆大人也在学习Python，这里做下记录，仅供总结和学习~



## 从Python的Object说起

Object是Python关于数据的抽象描述，在Python中，一切都是对象。对于每一个object，都有三样东西：`identity`，`type`和`value`。

* **Identity**
  * Identity是一个object的标识，从该对象创建时开始，就存在，**且不可改变**，我们可以通过`id()`函数来查看某一特定对象的identity(获取内存地址)。比如我们在Python中用的`is`函数，其实就是在比较两个比较对象的`id()`是否相同。
* **Type**
  * Type是该object的类型，它决定了该对象所具有的很多特性，比如是否可进行`+`操作，是否可进行`len()`等等。一个对象的Type也是已经建立，就**不可改变**的。
* **Value**
  * Value，这是一个object唯一可以改变的地方。在Python中，value可以改变的数据类型，被称为可变数据类型(**mutable**)；value不可改变的数据类型，称为不可变数据类型(**immutable**)。

变量从不会主动消失，当变量无用，变得不可到达时候，会通过Python的垃圾回收机制进行清空。Python使用以引用计数为主，代级回收为辅的机制进行垃圾回收。

## Value Type 和 Reference Type

在计算机编程语言中，不可避免的一个话题是变量。依据变量传递时候的不同表现，即 **传值** 和 **传引用**，可以将变量分为两大类：**Value Type** 和 **Reference Type**。

根据Wikipedia，可将一些语言中的数据类型根据这两种表现进行分类：

![language classify by var-type](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1537979872/For%20Blog/language-classify-by-var-type.png)

对于大多数计算机背景出身的人，第一门接触的语言应该是C Family Language，可以看到**C++**在这种分类场景下，除了别名(alias)和指针(pointers)是Reference Type，其他常见的数据类型都是Value Type。我相信你已经联想到了当时学C语言课上老师讲的形参等一系列概念。

我们看一下**Python**，很神奇的是它所有的数据类型都被划分到了Reference Type类别下。说到这里，我们就不得不提一下，**动态类型语言**和**静态类型语言**。

## 静态类型语言 和 动态类型语言

静态类型语言，是指变量的数据类型，在编译阶段就可以决定，这类语言，比如**C**、**C++**、**C#**、**Java**、**Scala**等等。而动态类型语言，是指变量的类型，要到程序运行时才可以确定，比如**JavaScript**、**PHP**、**Python**、**Ruby**等。

而大部分动态类型语言，在变量传递上，有一个共性——**Objective Reference**。

## Objective Reference

所谓Objective Reference，其实也可以理解为Reference Type的一种。它传递的，是**对象的引用**。

我们来看一个例子：

```python
def re_dict(x):
    x = x["data"]


def re_copy(x):
    y = x
    y["data"] = {
        "xxx": "xxx"
    }


if __name__ == '__main__':
    d = {
        "data": {
            "name": "lvxiaoxin",
            "company": "wecash"
        }
    }
    re_dict(d)
    print(d)
    re_copy(d)
    print(d)
```

这个程序的输出结果是：

```json
{'data': {'name': 'lvxiaoxin', 'company': 'wecash'}}
{'data': {'xxx': 'xxx'}}
```

是不是觉得很神奇，为什么第一个函数内的行为，没有影响到外面的**d**，但是第二个函数的行为，却影响到了。这是因为Python在参数传递时，采用了一种Objective Reference的形式。

在Python中，传入函数的变量，其实是一个对象引用。我们用三角代表引用(reference)，方块代表值(value)。在`re_dict(x)`这个函数中，其实传递进去的x，就是这里的三角，它指向一个方块，方块中的Value是`{'data': {'name': 'lvxiaoxin', 'company': 'wecash'}}`，如下图所示：

![ref_1](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1538062782/ref_1.png)

所以这里，在`re_dict(x)`函数内，x其实是一个变量，它的Value是一个指向方块的Reference。而代码中，`x = x["data"]`这一步操作，其实是，把这个x的Reference，指向了另外一块地方。即：

![ref_2](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1538063020/ref_2.png)

所以，当函数退出时，x随着生命周期结束而消失，但是这个保存Value的蓝色方块仍然存在，而我们的`d`，仍然指向这个方块。在整个过程中，蓝色方块始终没有变，`d`的指向也始终没有变，所以第一个函数结束后，`d`仍然保持不变。

第二个函数`re_copy(x)`中，在`y = x`这一部操作，就**体现了动态类型语言和静态类型语言的不同**。这一步操作如果发生在C Family Language中，那么效果应该是这样的：

![static](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1538063488/ref_3.png)

即新创建一个变量，名字叫y，且y的地址放在一个新的内存空间下。这种情况下，更改y，不会对x产生任何影响。

但是，对于Python这种动态类型语言，情况就有些不一样了：

![dynamic](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1538063654/ref_4.png)

Python并没有新创建一个变量y，而是给x所指向的Value，新增加了一个引用，也指向蓝色方块。所以，在`re_copy`中对y的一切操作，都真真实实的，影响在了蓝色方块Value所在的这块内存空间上，所以，当`re_copy`这个函数结束后，通过`d`去访问这块内存空间的Value的时候，会发现，已经变了。

##  顺便提一下Python中的深浅层复制

看这么一段代码：

```python
>>> a = [1, 2, 3]
>>> b = a
>>> a = [4, 5, 6]
>>> a
[4, 5, 6]
>>> b
[1, 2, 3]
# a 的值改变后，b 并没有随着 a 变

>>> a = [1, 2, 3]
>>> b = a
>>> a[0], a[1], a[2] = 4, 5, 6 //改变原来 list 中的元素
>>> a
[4, 5, 6]
>>> b
[4, 5, 6]
# a 的值改变后，b 随着 a 变了
```



我相信，在读完上面的解释之后，你会理解为什么上面的运行结果会有这么的反直觉，但是却很合理了。

那么如果我真的是，想要复制一份完全一样的数据，而且Value也放在全新的内存空间中去呢？可以向下面这么干：

```python
>>> a = [1, 2, 3]
>>> b = a.copy()
>>> a = [4, 5, 6] 
>>> a
[4, 5, 6]
>>> b
[1, 2, 3]
# a 的值改变后，b 并没有随着 a 变

>>> a = [1, 2, 3]
>>> b = a
>>> a[0], a[1], a[2] = 4, 5, 6 //改变原来 list 中的元素
>>> a
[4, 5, 6]
>>> b
[1, 2, 3]
# a 的值改变后，b 并没有随着 a 变
```

第一种，叫浅复制，第二种，叫深复制。

那如果我要复制一个较为复杂的object，它不像list、dict这样的内建数据类型一样有dict方法给我用怎么办？Python提供了一个内建模块`copy`，尝试使用`from copy import deepcopy`，你就会达到目的。

很多讨论深浅层复制的文章，在最开始都会说，深浅层复制在对于基本简单的，比如int这种数据类型时候没有差别。其实，不是没有差别，而是因为我们使用这种简单的数据时候都是直接使用类似于`y = x`这样的操作，而没有去修改这些Reference对应的内存空间中的Value。

所以，**Objective Reference**才是根本原因。

## 参考链接

* [Value type and reference type - Wikipedia](https://en.wikipedia.org/wiki/Value_type_and_reference_type)
* [Type System](https://en.wikipedia.org/wiki/Type_system)
* [Objects, values and types - Python Software Foundation](https://docs.python.org/3.6/reference/datamodel.html#objects-values-and-types)
* [Pythons pass-by-object-reference as explained by Philip](https://robertheaton.com/2014/02/09/pythons-pass-by-object-reference-as-explained-by-philip-k-dick/)
* 以及同事的解惑



> 献给我的老婆——娴 😁