# Day02：

### 1.为什么要用Redis？业务在哪块儿用到的？

​	Redis是眼下最为人熟知的缓解高并发、提升高可用能力的手段之一，在提升服务器性能方面效果显著。

​	这里不得不提到高并发场景，我们知道，并发场景下核心点在数据库，引入缓存（以及引入任何负载均衡、集群等策略）的目的都是在减轻数据库压力，让更多原本打到DB上的请求，在中间被拦截处理掉。

​	**Redis优势：**

* 读写性能优异，Redis能读的速度是110000次/s，写的速度是81000次/s。

* 支持数据持久化，支持AOF和RDB两种持久化方式。

* 支持事务，Redis的所有操作都是原子性的，同时Redis还支持对几个操作合并后的原子性执行。

* 数据结构丰富，除了支持string类型的value外还支持hash、set、szet、list等数据结构。

* 支持主从复制，主机会自动将数据同步到从机，可以进行读写分离。

* 支持大量集群节点。

  ​	假如用户第一次访问数据库中的某些数据。这个过程会比较慢，因为是从硬盘上读取的。将该用户访问的数据存在数Redis中，这样下一次再访问这些数据的时候就可以直接从缓存中获取了。同样，我们可以把数据库中的部分数据转移到缓存中，这样用户的一部分请求就可以直接从缓存中了。同样，我们可以把数据库中的部分数据转移到缓存中去，这样用户的一部分请求会直接打到缓存而不是数据库（即半路拦截掉了）。如果数据库中的对应数据改变之后，同步改变缓存中镶银的数据即可！

  ​	

**追问1：**Redis里有哪些数据类型？

丰富的数据类型，Redis有8种数据类型，当然常用的主要是String、Hash、List、Set、SortSet这五种类型，他们都是基于简直的方式组织数据。每一种数据类型提供了非常丰富的操作命令，可以满足绝大部分需求，如果有特殊需求还呢个自己通过lua脚本自己创建新的命令（具备原子性）；

| 1.速度快         | c语言实现，距离系统更近                      |
| ---------------- | -------------------------------------------- |
|                  | 数据都存在内存中                             |
|                  | 单线程，避免线程切换开销以及多线程的竞争问题 |
|                  | 采用epoll，非阻塞I/O，不在网络上浪费时间     |
| 支持多种数据类型 | String（字符串）                             |
|                  | Hash（哈希）                                 |
|                  | List（列表）                                 |
|                  | Set（集合）                                  |
|                  | ZSet（有序集合）                             |
|                  | Bitmaps（位图）                              |
|                  | HyperLogLog                                  |
|                  | GEO（地理信息定位）                          |

**追问2：**Redis与Memcached有哪些区别？

两者都是**非关系内存键值数据库**，一般都是用Redis来实现缓存。

| 参数         | Redis                                                        | Memcached                                                    |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 类型         | 1.支持内存    2.非关系型数据库                               | 1.支持内存；   2.键值对形式   3.缓存形式                     |
| 数据存储类型 | 1.String   2.List  3.Set  4.Hash  5.Sort Set                 | 1.文本型 2.二进制类型                                        |
| 附加功能     | 1.发布/订阅模式 2.主从分区 3.序列化支持 4.脚本支持【Lua脚本】 | 多线程服务支持                                               |
| 网络IO模型   | 单线程的多路IO复用模型                                       | 多线程，非阻塞IO模式                                         |
| 持久化支持   | 1.RDB  2.AOF                                                 | 不支持                                                       |
| 集群模式     | 原生支持cluster模式，可以实现主从复制，读写分离              | 没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据 |
| 内存管理机制 | 在Redis中，并不是所有数据都一直存储在内存中，可以将一些很久没用的value交换到磁盘 | Memcached的数据则会一直在内存中，Memcached将内存分割成特定长度的块来存储数据，以完全解决内存碎片的问题 |
| 适用场景     | 复杂数据结构，有持久化，高可用需求（value存储内容较大，最大512M） | 纯key-value，数据量非常大，并发量非常大的业务                |

* memcached所有的值均是简单的字符串，redis做为替代品，支持更为丰富的数据类型
* redis的速度比memcached快很多
* redis可以持久化数据到磁盘，这个很关键，宕机断电不再是硬伤

**追问3：**那Redis怎样防止异常数据不丢失？如何持久化？

**RDB持久化（快照）**

* 将某个时间点的所有数据生成快照，存放在硬盘上。当数据量很大时，会很慢。
* 可以将快照复制到其他服务器从而创建具有相同数据的服务器副本
* 如果系统发生故障，将会丢失最后一次创建快照之后的数据

**AOF持久化（即时更新）**

* 将写命令添加到AOF文件（Append Only File）的末尾
* 使用AOF持久化需要设置同步选项，从而确保命令同步到磁盘文件上的时机。这是因为对文件进行写入而不会马上将内容同步到磁盘，而是先存储到缓冲区，然后由操作系统觉得什么时候同步到磁盘

> 有以下同步选线（同步频率）：always每个写命令都同步；everysec每秒同步一次；no让操作系统来决定何时同步
>
> everysec选项比较合适，可以保证系统崩溃时只会丢失一秒左右的数据，并且Redis每秒执行一次同步对服务器性能几乎没有任何影响；

### 2.Redis为啥是单线程的？

​	因为Redis的瓶颈不是CPU的运行速度，而往往是网络宽带和机器的内存大小。单线程切换开销小，容易实现。既然单线程容易实现，而且CPU不会成为瓶颈，那就顺理成章地采用单线程的方案，当然了，也是为了避免多线程存在的很多坑。**一个节点是一个单线程。**

**追问1：**单线程只是用来单核CPU，太浪费了，有什么办法发挥多核CPU的性能吗？

在处理网络请求的时候只有一个线程来处理，实际上，一个正式的Redis Server运行的时候肯定是不止一个线程的，都是集群形式。

### 3.对缓存穿透、缓存击穿、缓存雪崩的理解

* 缓存穿透：指**缓存和数据库中都没有的数据**，导致所有的请求都打在数据库上，然后数据库还查不到（如null），造成数据库短时间线程数被打满而导致其他服务阻塞，最终导致线上服务不可用
* 缓存击穿：指**缓存中没有但数据库中有的数据**（一般是热点数据缓存时间到期），这是由于并发用户特别多，同时读缓存没读到数据，又同时去数据库去查，引起数据库压力瞬间增大，线上系统卡住。
* 缓存雪崩：指**缓存同一时间大面积的失效**，缓存击穿升级版。

**追问1：**针对缓存击穿的解决方法

1.根据实际业务情况，在Redis中维护一个热点数据表，批量设为永不过期（如top1000），并定时更新top1000数据

2.加互斥锁

> **互斥锁**
>
> ​	缓存击穿后，多个线程会同时去查询数据库的这条数据，那么我们可以在第一个查询数据的请求上使用一个互斥锁来所锁住它。
>
> ​	其他的线程走到这一步拿不到锁就等着，等第一个线程查询到数据，然后做缓存，后面的线程进来发现已经有缓存了，就直接走缓存。
