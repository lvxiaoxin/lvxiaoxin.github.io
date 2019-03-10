Python 垃圾回收机制



引用计数

 0 => 清除



循环引用问题



分代回收机制



Zero Generation：每当一个新的对象创建，就会被加入到0代，引用计数+1，这也是为何一开始创建对象引用计数就是2



![image-20190301110957531](/Users/xinlyu/Library/Application Support/typora-user-images/image-20190301110957531.png)

然后定期清理0代，

![image-20190301111026112](/Users/xinlyu/Library/Application Support/typora-user-images/image-20190301111026112.png)



引用计数减一，为0的清除，不是0 的，为活跃，移动到一代。



Python清除0代最频繁，1代次之，二代更次之。