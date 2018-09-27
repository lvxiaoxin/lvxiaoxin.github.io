# 从动态类型语言，到Python的Objective Reference

本系列文章着重描述`Python`语言中一些较为重要的话题，作为学习和记录。

> 献给我的老婆——娴。



## 从Python的Object说起

Object是Python关于数据的抽象描述，在Python中，一切都是对象。对于每一个object，都有三样东西：`identity`，`type`和`value`。



* **Identity**
  * Identity是一个object的标识，从该对象创建时开始，就存在，**且不可改变**，我们可以通过`id()`函数来查看某一特定对象的identity(获取内存地址)。比如我们在Python中用的`is`函数，其实就是在比较两个比较对象的`id()`是否相同。
* **Type**
  * Type是该object的类型，它决定了该对象所具有的很多特性，比如是否可进行`+`操作，是否可进行`len()`等等。一个对象的Type也是已经建立，就**不可改变**的。
* **Value**
  * Value，这是一个object唯一可以改变的地方。在Python中，value可以改变的数据类型，被称为可变数据类型(**mutable**)；value不可改变的数据类型，称为不可变数据类型(**immutable**)。

变量从不会主动消失，当变量无用，变得不可到达时候，会通过Python的垃圾回收机制进行清空。Python使用以引用计数为主，代级回收为辅的机制进行垃圾回收.

## Value Type 和 Reference Type

在计算机编程语言中，不可避免的一个话题是变量。依据变量传递时候的不同表现，即 **传值** 和 **传引用**，可以将变量分为两大类：**Value Type** 和 **Reference Type**。

根据Wikimedia，可将一些语言中的数据类型根据这两种表现进行分类：

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

在Python中，传入函数的变量，其实是一个对象引用。我们用三角代表





## 参考链接

* [Value type and reference type - Wikipedia](https://en.wikipedia.org/wiki/Value_type_and_reference_type)

* [Type System](https://en.wikipedia.org/wiki/Type_system)

* [Objects, values and types - Python Software Foundation](https://docs.python.org/3.6/reference/datamodel.html#objects-values-and-types)

* [Pythons pass-by-object-reference as explained by Philip](https://robertheaton.com/2014/02/09/pythons-pass-by-object-reference-as-explained-by-philip-k-dick/)
