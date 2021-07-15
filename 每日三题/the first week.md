### day01：

**1.Java中操作字符串都有哪些类？他们之间有什么区别？**

操作字符串的类有：String、StringBuffer、StringBuilder。

​	String和StringBuffer、StringBuilder的区别在于String声明的是不可变的对象，每次操作都会生成新的String对象，然后将指针指向新的String对象，而StringBuffer、StringBuilder可以在原有对象的基础上进行操作，所以在经常改变字符串内容的情况下最好不要使用String。

​	而StringBuffer和StringBUilder最大的区别在于，StringBuffer是线程安全的，而StringBuilder是非线程安全的，但StringBuilder的性能却高于StringBuffer，所以在单线程环境下推荐使用StringBuilder，多线程环境下推荐使用StringBuffer。

|              | String                                             | StringBuffer                                                 | StringBuilder                                        |
| ------------ | -------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------------------- |
| 类是否可变   | 不可变（Final）                                    | 可变                                                         | 可变                                                 |
| 功能介绍     | 每次String的操作都会在“常量池”中生成新的String对象 | 任何对它指向的字符串的操作都不会产生新的对象。每个StringBuffer对象都有一定的缓冲区容量，字符串大小没有超过容量时，不会分配新的容量，当字符串大小超过容量时，自动扩容 | 功能与StringBuffer相同，相比少了同步锁，执行速度更快 |
| 线程安全性   | 线程安全                                           | 线程安全                                                     | 线程不安全                                           |
| 使用场景退键 | 单次操作或循环外操作字符串                         | 多线程操作字符串                                             | 单线程操作字符串                                     |

**追问1：**这三者在效率上怎么说

StringBuilder > StringBuffer > String

String < （StringBuffer，StringBuilder）的原因？

* String：字符串常量
* StringBuffer：字符串变量（有同步锁）
* StringBuilder：字符串变量（无同步锁）

从上面的名字中可以看到，String是“字符串常量”，也就是不可改变的对象。源码如下：

```java
pubilc final class String{}
```

对于上面这句话的理解你可能会产生这样一个疑问，比如这段代码：











​	

