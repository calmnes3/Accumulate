# Day01：

## 1.Java中操作字符串都有哪些类？之间有什么区别？

**操作字符串的类有：String、StringBuffer、StringBuilder。**

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

String操作时内存变化图：

![在这里插入图片描述](C:\Users\xuweigang\Desktop\Accumulate\image\20210601173059844.png)

初始String值未“唐伯虎”，然后在这个字符串后面加上新的字符串“点香烟”，这个过程是需要重新在栈堆内存中开辟内存空间的，最终得到“唐伯虎点香烟”字符串也相应的需要开辟内存空间，这样短短两个字符串，却需要开辟三次内存空间，这是极大的浪费，执行效率同理

​	为了应对经常性操作字符串的场景，Java才提供其他两个操作字符串的类——StringBuffer、StringBuilder。

​	他们俩均属于字符串变量，是可改变的对象，每当我们用他们对字符串做操作时，实际上是在一个对象上操作，这样就不会像String一样创建一些而外的对象进行操作了，速度自然就相对快了。

​	我们一般在StringBuffer、StringBuilder类上的主要操作是append和insert方法，这些方法允许被重载，以接受任意类型的数据。每个方法都能有效地将给定的数据转换成字符串，然后将该字符串的字符追加或插入到字符串缓冲区中。append方法试始终将这些字符添加到缓冲区的末端；而insert方法则在指定的点（index）添加字符。

> 1.StringBuilder一个可变的字符序列是JDK1.5新增的。此类提供一个与StringBuffer兼容的API，但不保证同步。该类被设计用作StringBuffer的一个简单替换，用在字符串缓冲区被单个线程使用的时候（这种情况很普遍）。
>
> 2.如果可能，建议优先采用StringBuilder类，因为在大多数实现中，它比StringBuffer更快，且两者的方法基本相同，然后在应用程序要求线程安全的情况下，则必须使用StringBuffer类。

​	String类型和StringBuffer、StringBuilder类型的主要性能区别其实在于String是不可改变的对象（final），因此在每次对String类型进行改变的时候其实都等同于在堆中生成一个新的String对象，然后将指针指向新的String对象，这样不仅效率低下，而且大量浪费有限的内存空间，所以经常改变内容的字符串最好不要用String。因为每次生成对象都会对系统性能产生影响，特别是当内存中的无引用对象过多了之后，JVM的GC开始工作，那速度是一定会相当慢的。另外当GC清理速度跟不上new String的速度时，还会导致内存溢出Error，会直接kill掉主程序！

**追问2：**那StringBuffer和StringBuffer线程安全主要差在哪里呢？

线程安全方面，StringBUffer允许多线程进行字符操作。这是因为在源代码中StringBuffer的很多方法都被关键字synchronized修饰了，而StringBuilder没有。

> synchronized的含义：
>
> 每一个类对象都对应一把锁，当某个线程A调用类对象O中的synchronized方法M时，必须或的对象O的锁才能够执行M方法，否则线程A阻塞。一旦线程A开始执行M方法，将独占对象O的锁。使得其它需要调用O对象的M方法的线程阻塞。只有线程A执行完毕，释放锁后。那些阻塞线程才有机会重新调用M方法。这就是解决线程同步问题的锁机制。
>
> 如果有多个线程需要对同一个字符串缓冲区进行操作的时候，StringBuffer应该是不二人选。

注意：是不是String也不安全呢？事实上不存在这个问题，String是不可变的，线程对于堆中指定的一个String对象只能读取，无法修改

实际应用场景中：

1.如果不是在循环体中进行字符串拼接的话，直接使用String的“+”就好了；

2.单线程循环中操作大量字符串数据--->StringBuilder.append（）；

3.多线程循环中操作大量字符串数据--->StringBuffer.append（）；

## 2.Error和Exception区别是什么？

Error和Exception都是Throwable的子类，在Java中只有Throwable类型的实例才可以被抛出或者捕获，他是异常处理机制的基本类型。

![img](C:\Users\xuweigang\Desktop\Accumulate\image\20210601173214148.jpg)

1.Exception和Error体现了Java平台设计者对不同异常情况的分类，Exception是程序正常运行中，可以预料的意外情况，可能并且应该被捕获，进行相应的处理。

2.Error是指正常情况下，不大可能出现的情况，绝大部分的Error都会导致程序处于非正常的、不可恢复的状态。既然是非正常情况，不便于也不需要捕获。常见的比如OutOfMemoryError之类的都是Error的子类。

3.Exception又分为可检查（checked）异常和不可检查（unchecked）异常。可检查异常在源代码里必须显式的进行捕获处理，这是编译期检查的一部分。不可检查时异常是指运动时异常，像NullPointerException、ArrayIndexOutOfBoundsException之类，通常是可以编码避免的逻辑错误，具体根据需要来判断是否需要捕捉，并不会在编译期强制要求。

## 3.==和equals的区别是什么

* ==：它的作用是判断两个对象的地址是不是相等。即，判断两个对象是不是同一个对象。（基本数据类型 == 比较的是值，引用数据类型 == 比较的是内存地址）
* equals（）：它的作用也是判断两个对象是否相等。但他一般有两种使用情况“

情况1：类没有覆盖equals（）方法，则通过equals（）比较该类的两个对象时，等价于调用了Object类的equals（）方法，也就是通过“==”比较这两个对象。

```java
public boolean equals(Object obj){
    return (this == obj);
}
```

情况2：类覆盖equals（）方法。一般，我们都会覆盖equals（）方法来两个对象的内容相等；若他们的内容相等，则返回true（即，认定这两个对象相等）。

```java
public boolean equals(Object anObject){
	if(this == anObject){
        return true;
    }
    if(anObject instanceof String){
        String anotherString = (String)anObject;
        int n = value.length;
        if(n == anotherString.value.length){
            char V1[] = value;
            char V2[] = anotherString.value;
            int i = 0;
            while (n -- !=0){
                if (V1[i] != V2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

**追问1：**如果我们不重写equals()方法，会怎样？

```java
public static void main(String[] args){
    String a = "chenhaha";
    String b = "chenhaha";
    if (a == b){
        System.out.println("a==b");
    }
	StringBuffer c = new StringBuffer("chenhaha");
    StringBuffer d = new StringBuffer("chenhaha");
    if(c == d){
        System.out.println("c == d");
    }else{
        System.out.println("c != d");
    }
    if(c.equals(d)){
        System.out.println("StringBuffer equal true");
    }else{
        System.out.println("StringBuffer equal false")
    }
}
```

* object的equals方法是比较的对象的内存地址，而String的equals方法比较的时对象的值；
* 因为String中的equals方法是被重写过的，而StringBuilder没有重写equals方法，从而调用的是Object类的equals方法，也就相当于==；

**追问2：**重写equals的同时，需要重写hashCode()方法吗？

在重写equals()方法时，也有必要对hashCode方法进行重写，尤其是当我们自定义一个类，想把该类的实例存储在集合中时。

hashCode方法的常规约定为：值相同的对象必须有相同的hashCode，也就是equals()结果为相同，那么hashcode也要相同，equals()结果为不相同，那么hashCode也不相同；

当我们使用equals方法比较说明对象相同，但hashCode不同时，就会出现两个hashCode值，比如在HashMap中，就会认为这两个对象，因此会出现矛盾，说明equals方法和hashCode方法应该成对出现，当我们对equals方法进行重写时，也要对hashCode方法进行重写。































​	

