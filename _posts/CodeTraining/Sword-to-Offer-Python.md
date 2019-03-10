# Sword-to-Offer-Python

> 《剑指Offer》原书第二版收录的题目、拓展、相关问题 等的Python解法及关键思路。



## 面试题2 —— 单例

> 问题：实现单例模式
>
> 思路：类 继承 / 装饰器

**解法1**

```python
class Singleton(object):
    _instance = None
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super(Singleton, cls).__new__(cls, *args, **kwargs)
        return cls._instance

class A(Singleton):
    a = 1
    
if __name__ == "__main__":
    obj_0 = A()
    obj_1 = A()
    print(id(obj_0))
    print(id(obj_1))
```

**解法2**

```python
from functools import wraps

def singleton(cls):
    _instance = {}
    
    @wraps(cls)
    def get_singleton(*args, **kwargs):
        if cls not in _instance:
            _instance[cls] = cls(*args, **kwargs)
        return _instance[cls]
    
    return get_singleton

@singleton
class A(object):
    a = 1

if __name__ == "__main__":
    obj_0 = A()
    obj_1 = A()
    print(id(obj_0))
    print(id(obj_1))
```



## 面试题3 —— 二维数组中的查找

> 问题：二维数组，行、列均有序，给定二维数组和一个值x，判断x是否存在于该二维数组中
>
> 思路：找到一个起始点，边角，双向操作单调性上存在差异的点

```python
def solutions(array: list, x: int):
    if len(array) == 0:
        return False
    if len(array[0]) == 0:
        return False
    
    rows = len(array)
    columns = len(array[0])
    
    i = rows - 1
    j = 0
    
    while i >=0 and j <= columns-1:
        if array[i][j] == x:
            return True
        if array[i][j] > x:
            i -= 1
        if array[i][j] < x:
            j += 1
    
    return False

if __name__ == "__main__":
    print(solutions([[1, 2, 8, 9], [2, 4, 9, 12], [5, 7, 10, 13], [6, 8, 11, 15]], 15))
```



## 面试题4 —— 空格替换

> 问题：将给定字符串中，所有空格替换为 '%20'
>
> 思路：双指针法，同向，尾—>头倒向移动，减少移动次数
>
> tips: Python 中 list 的 append() 操作是 O(1) 的时间复杂度，所以可以一次遍历，遇到空格append操作即可。但会比下面的方法多出了一部分辅助空间的消耗。
>
> 时间复杂度：O(n)

```python
def solutions(s: str):
    if len(s) == 0:
        return s
    
    i = len(s)
    blank = s.count(' ')
    new_slot = blank * 2
    
    s += ' ' * new_slot
    j = len(s)
    
    s = list(s)
        
    while blank:
        if s[i-1] != " ":
            s[j-1] = s[i-1]
            j -= 1
            i -= 1
        elif s[i-1] == " ":
            s[j-3:j] = "%20"
            i -= 1
            j -= 3
            blank -= 1
    return "".join(s)

if __name__ == "__main__":
    solutions("hello world with me. ")
```



## 面试题4 - 相关题目 —— 合并排序数组

> 问题：合并两个排序数组，假定内存空间足够大
>
> 思路：同为双指针法（其实是三个指针）

```python
# 注意边界条件
def solutions(array_0: list, array_1: list):
    if len(array_0) == 0:
        if len(array_1) == 0:
            return []
        else: 
            return array_1
    else:
        if len(array_1) == 0:
            return array_0
        
    i = len(array_0)
    j = len(array_1)
    index = i + j

    
    array_0 += [None] * j
    while(i > 0 and j > 0):
        if array_0[i-1] >= array_1[j-1]:
            array_0[index-1] = array_0[i-1]
            i -= 1
            index -= 1
        if array_0[i-1] < array_1[j-1]:
            array_0[index-1] = array_1[j-1]
            j -= 1
            index -= 1
    return array_0

if __name__ == "__main__":
    solutions([-1, 3, 3, 3, 100], [0, 6, 8, 9])
```

**总结：灵活使用双指针法，双指针同向追赶（尾—>头），双指针相向相遇，通常都是关键结束点**



## 面试题5 —— 逆序输出单链表

> 问题：单链表，逆序输出
>
> 思路：循环 / 递归

```python
# link 维持一个 tail，使得 add() 函数的时间复杂度 O(n) => O(1)
class Node(object):
    def __init__(self, value, nextNode):
        self.value = value
        self.next = nextNode

class Linked(object):
    def __init__(self, head):
        self.head = head
        self.tail = head
        self.length = 0 if head is None else 1

    def show(self):
        current = self.head
        if current is None:
            return
        if current.next is None:
            print(current.value)
            return
        while(current != None):
            print(current.value)
            current = current.next
        return
    
    def add(self, node):
        self.tail.next = node
        self.tail = self.tail.next
        self.length += 1       

    # 递归 (Python 中，函数内部定义函数，递归写法变得简单)
    # 也可以顺序遍历，然后记录value在一个list中，list.pop() 天然的 stack
    def reverse_show(self):
        current = self.head
                
        def reverse(node):
            if node is None:
                return
            elif node.next is not None:
                reverse(node.next)
                print(node.value)
            else:
                print(node.value)
                
        reverse(current)
   
if __name__ == "__main__":
    begin = Node(0, None)
    link = Linked(begin)
    
    for i in range(1, 10):
        node = Node(i, None)
    	link.add(node)
        
    link.show()
    link.reverse_show()
```



## 面试题6 —— 重建二叉树

> 问题：给定 中序 和 前序 遍历结果，重建二叉树
>
> 思路：