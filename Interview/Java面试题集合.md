[TOC]

# 面试题集合

解决高并发要关注线程和I/O模型、数据库选型、缓存优化、缓冲区设计、解耦和消息队分布式服务设计等等方面的设计

LinkedHashMap是一个元素间用链表相连的哈希表

HashMap就是哈希表实现的Map; TreeMap就是用树实现的Map, Map是一种映射关系

AQS解决了Java语言没有实现同步原语底层框架的问题

B+树是一颗支持区间查找的B树

DNS是一个经典的分布式设计

BigTable是另一个

元编程是程序改写程序

系统性能发挥到最高

- CPU级别 - 总线锁和缓存一致性
- Java语言级别 - 利用线程池和消息异步回调 
- 中间件尽量使用Reactor模型
- 结构型数据库 - 要用到分库分表和集群
- 缓存集群要保证不可以雪崩和穿透
- 大量数据要学会使用数据湖 同时结合批处理和流式计算
- 搜索超大规模的数据 还要达到毫秒级别 使用ES云集群
- JVM的崩溃 使用G1垃圾回收器
- 大量日志的处理 使用大规模消息中间件集群
- 服务的数量众多 使用云技术动态扩展 
- 集中式的访问架构 要学会动静分离
- 对于复杂的逻辑 要实现简单的算法

## Buffer的原理和使用场景: 

流是数据的载体, 代表随时间产生的数据

缓冲区是数据临时存放的区域, 起缓冲作用

缓冲的本质是排队, 流的本质是是数据

没有缓冲: 拒绝服务; 性能低下

有缓冲: 排队处理; 批量处理

- 排队处理请求避免拒绝服务
- 批量写入磁盘快过多次写入
- 批量执行sql快过多次执行

数据拷贝的成本: 

- 网卡 >> 内核空间(DMA)
- 内核空间 >> 用户空间缓冲区
- 用户空间缓冲区 >> 处理线程

为什么不将设备的数据直接拷贝到用户空间

- 进程的隔离
- 内核是连接设备和进程的桥梁, 用户程序直接沟通设备非常危险

缓冲区的实现原理:

单向缓冲区:

- P: Position 下一个可写入的位置
- L: Limit 缓冲区实际的写入限制
- C: Capacity 缓冲区物理的写入限制

flip反转操作:将读操作转换写操作, Position变为0, Limit变为Position的位置

clear清空缓冲区操作:Position置为零, Limit置为Capacity

rewind操作: 重新读操作 将Position置为0

## 反射的概念

反射(reflection) 运行时查看、反观程序内部结构、甚至修改

- 运行时数据(通常称为元数据MetaData)   模块、类、函数、注解、源代码
- 反观自身
  - 通过字符串找到一个类, 并调用某个方法
  - 查看类的属性、方法
  - 查看注解
- 运行时修改   运行时修改的是bytecode

面向切面编程(Aspect Oriented Programming)

- 关注点分离原则(Separation of Concern) 
- 如何理解切面(Aspect) 
  - 程序有一个关注点 + 多个其他关注点(Aspect)
  - 可以配置主关注点和其他关注点一起工作

## 异常

常见的RuntimeException异常

- IllegalMonitorStateException
- NulPointerException
- ClassCastException
- IllegalArgumentException
- IndexOutOfBoundsException
- NumberFormatException
- ArrayStoreException
- UnsupportedOperationException

非RuntimeException

- ClassNotFoundException
- IOException

常见的Error

- UnsupportedClassVersionError

- NoClassDefFoundError

- StackOverflowError

- OutOfMemoryError

Spring中所有的异常基于org.springframework.core.NestedRuntimeException

try-catch的性能

Java异常处理消耗性能的地方

- try-catch块影响JVM的优化
- 异常对象实例需要保存栈快照等信息

Java的异常处理机制

- 抛出异常: 创建异常对象, 交由运行时系统处理
- 捕获异常: 寻找合适的异常处理器处理异常, 否者终止运行

Java异常处理原则

具体明确: 抛出的异常应能通过异常类名和message准确说明异常的类型和产生异常的原因

提早抛出: 应尽可能早的发现并抛出异常, 便于精准定位问题

延迟捕获: 异常的捕获和处理应尽可能延迟, 让掌握更多信息的作用域来处理异常

## Map

```java
// jdk14 HashMap::putVal
if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
    treeifyBin(tab, hash);
break;
```

```java
// jdk14 ConcurrentHashMap::putVal
if (binCount >= TREEIFY_THRESHOLD)
    treeifyBin(tab, i);
```

```java
// jdk14 HashMap
static final int tableSizeFor(int cap) {
    int n = -1 >>> Integer.numberOfLeadingZeros(cap - 1);
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}

@HotSpotIntrinsicCandidate
public static int numberOfLeadingZeros(int i) {
    // HD, Count leading 0's
    if (i <= 0)
        return i == 0 ? 32 : 0;
    int n = 31;
    if (i >= 1 << 16) { n -= 16; i >>>= 16; }
    if (i >= 1 <<  8) { n -=  8; i >>>=  8; }
    if (i >= 1 <<  4) { n -=  4; i >>>=  4; }
    if (i >= 1 <<  2) { n -=  2; i >>>=  2; }
    return n - (i >>> 1);
}
@Native public static final int   MIN_VALUE = 0x80000000;
public static int highestOneBit(int i) {
    return i & (MIN_VALUE >>> numberOfLeadingZeros(i));
}
```

```java
// jdk7 HashMap
private static int roundUpToPowerOf2(int number) {
    // assert number >= 0 : "number must be non-negative";
    return number >= MAXIMUM_CAPACITY
        ? MAXIMUM_CAPACITY
        : (number > 1) ? Integer.highestOneBit((number - 1) << 1) : 1;
}
public static int highestOneBit(int i) {
        // HD, Figure 3-1
        i |= (i >>  1);
        i |= (i >>  2);
        i |= (i >>  4);
        i |= (i >>  8);
        i |= (i >> 16);
        return i - (i >>> 1);
}
```

### HashMap: put方法的逻辑(jdk14)

- 如果HashMap未被初始化过, 则初始化
- 对key求hash, 计算下标
- 如果没有碰撞, 直接放入桶中
- 如果发生碰撞, 以链表的方式链接在后面
- 如果链表长度超过阈值, 则树化
- 如果链表长度低于6, 则链表化
- 如果节点已存在则替换旧值
- 如果桶满了(threshold = 容量 * 加载因子), 则扩容 resize 2倍

如何有效减少碰撞

- 使用扰动函数, 促使元素位置分布均匀, 减少碰撞几率
- 使用final对象并采用合适的equals()和hashCode()方法

扩容问题

- 多线程环境下: 调整大小会存在条件竞争, 容易造成死锁
- rehashing是一个比较耗时的过程

### ConcurrentHashMap: put方法的逻辑(jdk14)

- 判断Node[]数组是否初始化, 没有则进行初始化
- 通过hash定位数组的索引坐标, 是否有Node节点, 如果没有则使用CAS进行添加(链表的头节点), 添加失败则进入下一次循环
- 若检查到内部正在扩容, 则帮助它一块扩容(helpTransfer)
- 如果链表的头节点不为空, 则使用synchronized锁住头节点(链表、红黑二叉树的头元素)
  - 如果是Node(链表结构), 则执行链表的添加操作
  - 如果是TreeNode(树形结构), 则执行树添加操纵
  - 如果ReservationNode(和ConcurrentHashMap的本地缓存相关) 则抛出异常
- 判断链表长度已经达到临界值8, 若是则树化

CAS + synchronized 比jdk7使用Segment 锁拆的更细, 性能提升了很多

首先使用无锁操作CAS插入头节点, 失败则循环重试

若头节点已存在, 则尝试获取头节点的同步锁, 再进行操作

ConcurrentHashMap博大精深(50多个内部类)

注意点: 

- ConcurrentHashMap中的size()方法和mappingCount()方法的异同, 两者计算是否准确
- 多线程下如何进行扩容

## J.U.C知识梳理

JUC包的分类

- 线程执行器executors
- 锁locks
- 原子变量atomic
- 并发工具类tools
- 并发集合collections

### tools

- CountDownLatch
- CyclicBarrier
- Semaphore
- Executors
- Phaser
- Exchanger

### locks

- Lock
  - ReentrantLock
  - ReentrantReadWriteLock.ReadLock
  - ReentrantReadWriteLock.WriteLock

- Condition
- ReadWriteLock
  - ReentrantReadWriteLock
- LockSupport

### atomic

基本数据类型

- AtomicBoolean
- AtomicInteger
- AtomicLong

数组

- AtomicIntegerArray
- AtomicLongArray
- AtomicReferenceArray

引用类型

- AtomicReference
- AtomicStampedReference
- AtomicMarkableReference

对象属性更新

- AtomicLongFieldUpdater
- AtomicIntegerFieldUpdater
- AtomicReferenceFieldUpdater

累加器

- DoubleAccumulator
- DoubleAdder
- LongAccumulator
- LongAdder


### collections

- Queue
  -  ConcurrentLinkedQueue
  - BlockingQueue
    - ArrayBlockQueue
    - DelayQueue
    - LinkedBlockingQueue
    - PriorityBlockingQueue
    - SynchronousQueue
  - Deque
    - ArrayDeque
    - LinkedList
    - BlockingDeque
      - LinkedBlockingQueue
- CopyOnWriteArrayList
- CopyOnWriteArraySet
- ConcurrentSkipSet
- ConcurrentMap
  - ConcurrentHashMap
  - ConcurrentNavigableMap
    - ConcurrentSkipListMap

### executor

- Future
  - RunnableFuture
    - RunnableScheduleFuture
    - FutureTask
  - ScheduledFuture
- Callable
- Executor
  - ExecutorService
    - ScheduledExecutorService
      - ScheduledThreadPoolExecutor
    - ThreadPoolExecutor
- CompletionService
  - ExecutorCompletionService
- RejectedExecutionHandler
  - ThreadPoolExecutor.DiscardPolicy
  - ThreadPoolExecutor.DiscardOldestPolicy
  - ThreadPoolExecutor.CallerRunsPolicy
  - ThreadPoolExecutor.AbortPolicy
- TimeUnit

## Java的IO机制

ServerSocket是一个文件, 存放着所有连进来的Client FD

### I/O多路复用

I/O多路复用底层主要用的是 Linux 内核函数 (select、poll、epoll) 来实现; windows 不支持epoll 实现; windows 底层是基于winsock2的selec函数实现的(不开源)

select、poll、epoll的区别

|        | 支持一个进程所能打开的最大连接数                             | FD剧增后带来的IO效率问题                                     | 消息传递方式                                    | 操作方式 | 底层实现 |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------------- | -------- | -------- |
| select | 单个进程所能打开的最大连接数由FD_SETSIZE宏定义, 其大小是32个整数的大小(在32位机器上大小是32＊32, 64位机器则是32 * 64) 可以对其进行修改, 但是需要重新编译内核 其性能也无法保证 | 因为每次调用时都会对连接进行线性遍历, 所以随着FD的增加会造成遍历速度的线性下降的性能问题 | 内核需要将消息传递到用户空间 需要内核的拷贝动作 | 遍历     | 数组     |
| poll   | 本质和select没有区别, 但是它没有最大连接数的限制, 其原因是它是基于链表来储存的 | 同上                                                         | 同上                                            | 遍历     | 链表     |
| epoll  | 虽然连接数有上限, 但是很大; 1G内存的机器可以打开10万左右的连接 | 由于epoll是根据每个FD上的回调函数来实现的, 只有活跃的的socket才会主动调用callback,所以在活跃socket较少的情况下, 使用epoll不会有线性下降的性能问题, 但是所有socket都活跃的情况下 可能会有性能问题 | 通过内核和用户空间共享一块内存来实现 性能较高   | 回调     | 哈希表   |

单个线程处理多个Socket

- 核心问题: 内核分发消息
- select模型: 线程维护一个Socket FD的列表
- epoll模型: 内核维护一个高效的二叉搜索树

性能提升归根结底是算法和数据结构, 并不是同步异步以及阻塞和非阻塞的原因

### BIO

Block-IO InputStream和OutputStream; Reader和Writer

同步阻塞

API设计: 操作会阻塞线程(让线程休眠)

原理: 利用CPU的中断

- 阻塞的线程进入休眠, 将执行权限交给其他线程
- 优点: 阻塞时不会占用系统资源, 程序好理解
- 缺点: 高并发场景需要更多的线程资源, 线程的休眠唤醒需要成本

适用范围 一般的高并发场景可以考虑(并行量特别大的场景除外)

### NIO

![20211018151300](https://cdn.qingweico.cn/blog/20211018151300.png)

jdk1.4中引入了NIO

NonBlock-IO: 构建多路复用的, 同步非阻塞的IO操作

API设计: 操作不会阻塞线程, 读不到就返回null

特点: 程序需要不断地去主动询问内核数据是否已准备好

NIO的核心

- Channels
- Buffers
- Selectors

Channel

- FileChannel
  - transferTo: 把FileChannel中的数据拷到另外一个Channel
  - transferFrom: 把另外一个Channel中的数据拷贝到FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel

避免了两次用户态到内核态的切换 即"零拷贝" 效率高

Buffers

- 八大数据类型除了String外都有各自对应的Buffer
- MappedByteBuffer: 内存映射文件

Selectors

### AIO

原理: 由专门的数据结构负责统计发生的I/O 再允许程序监听变更

API设计: 异步编程; 基于Future

原理 

- 利用线程池技术、协程技术调度所有Future计算

本质

-  异步转同步

AIO如何进一步加工处理结果

- 基于回调: 实现CompletionHandler接口
- 返回Future: 通过isDone()查看是否准备好, 通过get() 等待返回数据

通常结合epoll和directmemory技术

directmemory: DirectMapping

由内核创建一个堆外的内存, 再将堆外的内存给到JVM进程, 当数据到达网卡时, 由内核将数据拷贝至堆外缓冲区

本质: 堆外的缓冲区; 优点: 减少一次拷贝

| 属性/模型                | 阻塞     | 非阻塞NIO  | 异步AIO    |
| :----------------------- | -------- | ---------- | ---------- |
| blocking                 | 阻塞同步 | 非阻塞同步 | 非阻塞异步 |
| 线程数(server :  client) | 1:1      | 1:N        | 0:N        |
| 复杂度                   | 简单     | 较复杂     | 复杂       |
| 吞吐量                   | 低       | 高         | 高         |

## 数据结构

链表是一种抽象(有很多的实现)的数据结构聚合(数据和操作的容器)

## JVM

栈的作用是配合执行程序, 提供执行程序的必须内存空间

栈储存程序执行时的临时数据

### 吞吐量

吞吐量(Throughput): 程序工作时间占比(没有STW: GC没有占用CPU的时间)

多线程GC不一定能够提高Throughput

```properties
# JVM参数 控制吞吐量
-XX: GCTimeRatio=99
```

JVM参数

- 标准参数  -classpath
- -X 扩展参数(非标准; 不保证每个JVM都支持) -Xmx256m 设置堆的最大大小
- -XX 开发用(比如打印详细日志) -XX: +PrintGCDetails

### Latency

指GC造成的停顿时间(STW时间)

- Pause Time: 一次STW的时间
- 吞吐量大并不一定代表延迟就低, 取决于Latency是否集中
- 多线程可以减少Latency
- 内存更大也可以减少Latency

### FootPrint

指最终应用对内存的需求

内存使用100M/s 回收速度80M/s 每10s一次GC(STW全部回收)  >> FootPrint: 200M

假如GC可以无限使用CPU >> FootPrint 可以很低, 而延迟却很高

假如每次GC可以不中断 >> 高延迟

阿姆达定律是衡量并行计算的重要依据

### GC算法

引用计数

Root Tracing

```shell
# 查看GC参数
java -XX:+PrintCommandLineFlags -version
```

### 垃圾回收器

#### Serial

使用的算法是Root Tracing & Mark-Sweep; 单线程, GC完才能继续

```properties
# 使用 serial GC
-XX:+UseSerialGC
```

适用的场景 

- 吞吐量小, 内存回收量不大
- 容忍延迟, 不在意卡顿
- 单核, 内存小, 0~100M

#### Serial Old

#### Parallel(并行) 

提供最大的Throughput

多线程GC完才能继续

```properties
# 使用 ParallelGC
-XX:+UseParallelGC
```

算法: Root Tracing & Mark - Sweep

场景: 吞吐量要求 > 延迟要求

#### Parallel Old

#### ParNew

#### Parallel Scavenge

#### Concurrent Mark Sweep(CMS)

减少Pause Time

#### G1

#### ZGC

#### Shenandoah

#### Epsilon

### GC 的分代模型与分区模型

### JVM调试工具

#### Jmeter

#### jps

- jps -l  : 输出应用程序主类的完整包名或应用程序 jar文件的完整路径名

#### jstat

Java Virtual Machine statistics monitoring tool Java统计监控工具

eg

- man jstat

- jstat -gcutil  pid: 显示关于垃圾收集的摘要 统计数据  

#### jstatd

jstat的守护进程 

RMI服务器应用程序

[[政策文件语法](http://docs.oracle.com/javase/1.5.0/docs/guide/security/PolicyFiles.html)](https://docs.oracle.com/javase/1.5.0/docs/guide/security/PolicyFiles.html)

#### jcmd

eg

- jcmd  pid GC.heap_info
- jcmd pid VM.flags -all 查看虚拟机参数

#### jmap

- Java Memory Map
- 打印JVM实例的共享对象映射、堆内详细信息(支持远程调试)

eg:

- jmap -dump:live,format=b,file=heap.bin pid
- jhat heap.bin(Started HTTP server on port 7000)

#### jhat

分析java堆的命令, 可以将堆中的对象以html的形式显示出来 配合jmap命令使用

#### jinfo 

- Java Configuration Info
- 查看修改虚拟机配置(支持远程)

eg: 

- jinfo pid 查看pid参数配置
- jinfo -flag -XX:InitialHeapSize=536870912=536870912 pid(flag '-XX:InitialHeapSize' cannot be changed)

#### jstack

- Java Stack Tace
- 打印Java的Srtack(支持远程调试)

eg:

- jstack pid 

#### jconsole

### invoke调用

- invokespecial call super(), private, init()
- invokedynamic call lambda +- 可以绕过检查、限制, 提高性能
- invokestatic call static method
- invokevirtual
  - 虚函数: 可以重写的函数
  - static private final 除外
- invokeinterface call interface

#### MAT(Memory Analyzer tool)

MAT 是一种快速, 功能丰富的Java堆分析工具, 能帮助你查找内存泄漏和减少内存消耗

[Memory Analyzer tool下载链接](https://www.eclipse.org/mat/downloads.php)

功能

- 内存分布详情
- 对象间依赖
- 对象状态详情
- 按条件检索

### 对象的生命周期

- load 

  - ClassLoader

  - static initializer/ static fields

  - ```java
    static {}
    ```

  - 触发原因: new/访问静态资源/loader加载等

  - finish >> loaded

- create

  - allocate memory
  - constructor
  - finish >> created

- live

  - in use  GC Root可以找到, 且被使用
  - invisible 泄漏 GC Root 可以找到, 但是没被使用
  - unreachable GC Root 不可达(会被回收)

- destroy/gc

  - collected
  - finalize(终结)
  - deallocated

## 线程池的作用

- 降低资源消耗: 通过池化技术重复利用已创建的线程, 降低线程创建和销毁造成的损耗
- 提高响应速度: 任务到达时, 无需等待线程创建即可立即执行
- 提高线程的可管理性: 线程是稀缺资源, 如果无限制的创建, 不仅会消耗系统资源还会因为线程的不合理分布导致资源调度失衡, 降低系统的稳定性; 使用线程池可以进行统一的分配、调优和监控
- 提供更多更强大的功能: 线程池具备可扩展性, 允许开发人员向其中增加更多的功能

不建议使用Executors: 因为默认的Executors线程池底层是基于ThreadPoolExecutor构造函数封装的, 采用无界队列存放缓存任务, 会无限缓存任务容易发生内存溢出, 会导致最大的线程数失效

#### ThreadPoolExecutor的继承关系

![图1 ThreadPoolExecutor UML类图](https://cdn.qingweico.cn/blog/912883e51327e0c7a9d753d11896326511272.png)

#### 线程池中线程的生命周期状态

![img](https://cdn.qingweico.cn/blog/62853fa44bfa47d63143babe3b5a4c6e82532.png)

#### ctl属性分析

ctl本质上就是一个int类型的数值 其被切割为高3位和低29位两种状态

其中高位表示线程池当前的状态 而低位则表示线程池当前的工作线程个数

```java
private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
// 29
private static final int COUNT_BITS = Integer.SIZE - 3;
// 00011111 11111111 11111111 11111111
private static final int COUNT_MASK = (1 << COUNT_BITS) - 1;

// runState is stored in the high-order bits
// 111(只看高3位)
private static final int RUNNING    = -1 << COUNT_BITS;
// 000(只看高3位)
private static final int SHUTDOWN   =  0 << COUNT_BITS;
// 001(只看高3位)
private static final int STOP       =  1 << COUNT_BITS;
// 010(只看高3位) tidying: 整齐的;有条理的
private static final int TIDYING    =  2 << COUNT_BITS;
// 011(只看高3位)
private static final int TERMINATED =  3 << COUNT_BITS;

// Packing and unpacking ctl
// 计算当前线程池的工作状态
private static int runStateOf(int c)     { return c & ~COUNT_MASK; }
// 计算当前线程池中的工作线程个数
private static int workerCountOf(int c)  { return c & COUNT_MASK; }
private static int ctlOf(int rs, int wc) { return rs | wc; }
```

#### 生命周期转换(线程池的状态变换)

RUNNING

- 正常接受任务
- 可以处理任务
- 处理任务队列中的任务

SHUTDOWN

- 不接受任务
- 可与处理任务
- 处理任务队列中的任务
- (内部自动)调用tryTerminated() 方法进入到DIDYING状态

STOP

- 不接受任务
- 中断正在被处理的任务
- 不处理任务队列中的任务
- (内部自动)调用tryTerminated() 方法进入到DIDYING状态

DIDYING

- 过度状态
- (内部自动)调用terminated()进入到TERMINATED状态

TERMINATED



![图3 线程池生命周期](https://cdn.qingweico.cn/blog/582d1606d57ff99aa0e5f8fc59c7819329028.png)

#### 拒绝策略

![9ffb64cc4c64c0cb8d38dac01c89c905178456](https://cdn.qingweico.cn/blog/9ffb64cc4c64c0cb8d38dac01c89c905178456.png)

#### execute方法

```java
public void execute(Runnable command) {
    	// 非空判断
        if (command == null)
            throw new NullPointerException();
        int c = ctl.get();
        // 工作线程的个数是否小于核心线程数
        if (workerCountOf(c) < corePoolSize) {
            // 小于 则添加核心线程执行command任务 直接返回
            if (addWorker(command, true))
                return;
            // 如果在并发情况下 添加核心线程失败的线程 需要重新获取一次ctl属性
            c = ctl.get();
        }
    	// 创建核心线程失败
   		// 判断当前线程池的状态是否是RUNNING
    	// 如果是RUNNING 则执行offer方法添加任务到工作队列
        if (isRunning(c) && workQueue.offer(command)) {
            // 添加任务到工作队列成功
            int recheck = ctl.get();
            // 判断当前线程池的状态是否是RUNNING
            // 如果不是RUNNING状态 需要将任务从工作队列移除 然后执行拒绝策略
            if (!isRunning(recheck) && remove(command))
                // 执行拒绝策略
                reject(command);
            // 判判工作线程是否为0
            else if (workerCountOf(recheck) == 0)
                // 工作线程为0 但是工作队列中有任务在排队
                // 添加一个空任务非核心线程 为了处理在工作队列排队的任务
                addWorker(null, false);
        }
    	// 添加任务队列失败
    	// 创建非核心线程去执行当前任务
        else if (!addWorker(command, false))
            // 创建非核心线程失败 则执行拒绝策略
            reject(command);
    }
```

#### andWorker()

```java
private boolean addWorker(Runnable firstTask, boolean core) {
    // ################################### 对线程池状态的判断 ###################################
    retry:
    // 获取ctl的值	
    for (int c = ctl.get();;) {
        // 如果线程池不是RUNNING状态 则进行后续判断
        if (runStateAtLeast(c, SHUTDOWN)
            // 如果线程池状态为SHUTDOWN 且任务为空 并且任务队列不为空
            // 如果同时满足这三个要求 则表明需要处理工作队列中的任务 
            // 不能return false 需要走后面逻辑
            // 如果有任何一个条件不满足 则直接return false
            && (runStateAtLeast(c, STOP)
                || firstTask != null
                || workQueue.isEmpty()))
            return false;

        // ################################### 对线程池数量的判断 ################################### 
        for (;;) {
            // 判断工作线程是否达到最大值
            if (workerCountOf(c)
                >= ((core ? corePoolSize : maximumPoolSize) & COUNT_MASK))
                // 达到最大值 直接返回false 添加失败
                return false;
            
            // CAS 添加工作线程数量
            if (compareAndIncrementWorkerCount(c))
                // 添加成功 直接跳出外层循环
                break retry;
            // 添加失败 存在并发
            // 重新获取ctl的值
            c = ctl.get();  // Re-read ctl
            // 用最新的ctl的值 判断线程池状态是否发生变化
            if (runStateAtLeast(c, SHUTDOWN))
                // 线程池状态不是RUNNING 需要重新跳到外层循环重新判断
                continue retry;
            // 线程池状态是RUNNING 没有发生变化 只需在内层循环判断工作线程数量
            // else CAS failed due to workerCount change; retry inner loop
        }
    }
   // ################################### 添加工作线程 并启动工作线程 ###################################
    
    // 工作线程是否启动
    boolean workerStarted = false;
    // 工作线程是否添加	
    boolean workerAdded = false;
    // Worker类就是工作线程 继承了 AQS
    Worker w = null;
    try {
        // new Worker 构建工作线程 将任务放进Worker中
        w = new Worker(firstTask);
        final Thread t = w.thread;
        // 肯定不为null(健壮性判断)
        if (t != null) {
            final ReentrantLock mainLock = this.mainLock;
            mainLock.lock();
            try {
                // Recheck while holding lock.
                // Back out on ThreadFactory failure or if
                // shut down before lock acquired.
                // 重新获取ctl
                int c = ctl.get();
				// 如果线程池状态为RUNNING 就可以添加工作线程
                if (isRunning(c) ||
                    // 如果线程池状态为SHUTDOWN 并且传入的任务为null
                    (runStateLessThan(c, STOP) && firstTask == null)) {
                    // 如果线程不是处理就绪状态(即没有调用过start()方法) 抛出异常
                    if (t.getState() != Thread.State.NEW)
                        throw new IllegalThreadStateException();
                    
                    // 将构建好的Worker对象添加到workers中
                    workers.add(w);
                    // 标志worker已添加
                    workerAdded = true;
                    // 获取工作线程个数
                    int s = workers.size();
                    // 记录线程池生命周期中最大的工作线程数
                    if (s > largestPoolSize)
                        largestPoolSize = s;
                }
            } finally {
                mainLock.unlock();
            }
            if (workerAdded) {
                // 启动工作线程
                t.start();
                // 标识工作线程已启动
                workerStarted = true;
            }
        }
    } finally {
        // 如果启动工作线程失败
        if (! workerStarted)
            addWorkerFailed(w);
    }
    return workerStarted;
}
 
private void addWorkerFailed(Worker w) {
    final ReentrantLock mainLock = this.mainLock;
    mainLock.lock();
    try {
        if (w != null)
            // 如果之前的工作线程创建成功 则移除从workers
            workers.remove(w);
        // CAS 将工作线程数量减1
        decrementWorkerCount();
        // 尝试将线程池状态变为 TIDYing
        tryTerminate();
    } finally {
        mainLock.unlock();
    }
}
```

#### Worker对象

```java
private final class Worker
    extends AbstractQueuedSynchronizer
    implements Runnable
{
    private static final long serialVersionUID = 6138294804551838833L;
    final Thread thread;
    // 需要执行的任务
    Runnable firstTask;
    volatile long completedTasks;
    Worker(Runnable firstTask) {
        // 刚刚初始化的工作线程 不允许被中断 直到执行runWorker方法 将state变为0
        setState(-1); 
        this.firstTask = firstTask;
        this.thread = getThreadFactory().newThread(this);
    }
    // start()方法执行
    public void run() {
        runWorker(this);
    }
    protected boolean isHeldExclusively() {
        return getState() != 0;
    }
    protected boolean tryAcquire(int unused) {
        if (compareAndSetState(0, 1)) {
            setExclusiveOwnerThread(Thread.currentThread());
            return true;
        }
        return false;
    }
    protected boolean tryRelease(int unused) {
        setExclusiveOwnerThread(null);
        setState(0);
        return true;
    }
    public void lock()        { acquire(1); }
    public boolean tryLock()  { return tryAcquire(1); }
    public void unlock()      { release(1); }
    public boolean isLocked() { return isHeldExclusively(); }
    void interruptIfStarted() {
        Thread t;
        if (getState() >= 0 && (t = thread) != null && !t.isInterrupted()) {
            try {
                t.interrupt();
            } catch (SecurityException ignore) {
            }
        }
    }
}
```

#### runWorker() 

```java
final void runWorker(Worker w) {
    Thread wt = Thread.currentThread();
    Runnable task = w.firstTask;
    w.firstTask = null;
    // 将Worker中state归为0 代表可以被中断
    w.unlock(); // allow interrupts
    /// 标识执行过程中有没有出现异常
    boolean completedAbruptly = true;
    try {
        // 获取任务的两种方式
        // 1 线程池执行exec submit方法 传入的任务
        // 2 从任务队列中获取任务
        while (task != null || (task = getTask()) != null) {
            // 加锁的原因
            // 1 在 SHUTDOWN状态下 当前线程不允许被中断
            // 2 由于Worked内部的锁实现是基于AQS的 不支持重入
            // 3 因为在中断时 也需要对worker进行lock 不能获取就代表当前工作线程正在执行任务
            w.lock();
            // 如果线程池状态变为了STOP 状态 必须将当前线程中断
            // 判断线程池状态是否为 STOP
            // 如果是 STOP 状态 且没有被中断 则将中断标记为设置为true
            if ((runStateAtLeast(ctl.get(), STOP) ||
                 // 如果不是 STOP 状态 查看是否被中断并清除中断标识位 
                 // 若标志位为false则说明不是 STOP 状态
                 // 如果标志位为true需要再次查看是否存在并发操作导致线程池状态变为了STOP
                 (Thread.interrupted() && runStateAtLeast(ctl.get(), STOP))) 
                && !wt.isInterrupted())
                wt.interrupt();
            try {
                // 重写扩展
                beforeExecute(wt, task);
                try {
                    task.run();
                    afterExecute(task, null);
                } catch (Throwable ex) {
                     // 重写扩展
                    afterExecute(task, ex);
                    throw ex;
                }
            } finally {
                // 清空task
                task = null;
                w.completedTasks++;
                w.unlock();
            }
        }
        completedAbruptly = false;
    } finally {
        processWorkerExit(w, completedAbruptly);
    }
}
```

#### getTask()

```java
private Runnable getTask() {
    // 超时后 非核心线程可以干掉
    boolean timedOut = false; // Did the last poll() time out?

    for (;;) {
        int c = ctl.get();
		// ################################### 判断线程池状态 ###################################
        // 假如线程池状态为 SHUTDOWN 或者 STOP
        if (runStateAtLeast(c, SHUTDOWN)
            // 1 假如线程池状态为 STOP 需要移除掉当前工作线程
            // 2 假如线程池状态为 SHUTDOWN 并且工作队列为空 需要移除掉当前工作线程
            && (runStateAtLeast(c, STOP) || workQueue.isEmpty())) {
            // CAS 工作线程数量减 1
            decrementWorkerCount();
            // processWorkerExit() 会移除当前线程
            return null;
        }
        // ################################### 判断线程池数量 ###################################  
        int wc = workerCountOf(c);

        // allowCoreThreadTimeOut 是否允许核心线程超时
        // 判断工作线程数量是否大于核心线程 allowCoreThreadTimeOut 默认为 fasle
        boolean timed = allowCoreThreadTimeOut || wc > corePoolSize;
		// 判断工作线程数量是否大于最大线程数
        if ((wc > maximumPoolSize || 
             // 工作线程数量是否大于核心线程(timed = true)且当前线程已经超时((timedOut = true))
             (timed && timedOut))
            // 工作线程数大于1 或者工作队列为空 意思就是: 只要队列中没任务或者还有别的线程活着, 我就可以死了
            && (wc > 1 || workQueue.isEmpty())) {
            // CAS 工作线程减 1
            if (compareAndDecrementWorkerCount(c))
                // processWorkerExit() 会移除当前线程
                return null;
            continue;
        }
        // ################################### 从工作队列中获取任务 ###################################
        try {
            // timed为true 允许核心线程超时或者工作线程大于核心线程了
            // 阻塞一段时间keepAliveTime 从工作队列拿任务
            Runnable r = timed ?
                workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS) :
            workQueue.take();
            if (r != null)
                // 拿到任务直接return
                return r;
            // ######################################################################
            // 其实这里不再区分核心和非核心线程了 开始添加到Workers中的Worker只是时间上的先后顺序不同 即核心
            // 和非核心线程的创建时间不同罢了 都是为了完成任务的 并不是说核心线程一旦创建就永远是核心线程 就永远
            // 不会被回收掉
            timedOut = true;
            // 线程获取任务超时了和线程被打断
            // 都会使 timedOut 变为 true 重现执行循环 有机会干掉自己
        } catch (InterruptedException retry) {
            timedOut = false;
        }
    }
}
```

#### processWorkerExit()

```java
private void processWorkerExit(Worker w, boolean completedAbruptly) {
    if (completedAbruptly) // If abrupt, then workerCount wasn't adjusted
        decrementWorkerCount();

    final ReentrantLock mainLock = this.mainLock;
    mainLock.lock();
    try {
        // 记录当前线程池完成的任务数量
        completedTaskCount += w.completedTasks;
        // 移除当前工作线程
        workers.remove(w);
    } finally {
        mainLock.unlock();
    }

    // 尝试将线程池设置为TIDYING状态
    tryTerminate();

    // 获取 ctl
    int c = ctl.get();
    // 当前的线程池状态是不是 RUNNING 或者 SHUTDOWN
    if (runStateLessThan(c, STOP)) {
        // 正常情况下 completedAbruptly 是 false
        if (!completedAbruptly) {
            int min = allowCoreThreadTimeOut ? 0 : corePoolSize;
            // 如果工作队列中还有任务则设置工作线程的最小值为1
            if (min == 0 && ! workQueue.isEmpty())
                min = 1;
            // 假如
            if (workerCountOf(c) >= min)
                return; 
        }
        // 发生异常了 移除了当前工作线程 再添加一个工作线程
        // 线程池工作队列不为空 并且没有工作线程 再添加一个工作线程
        addWorker(null, false);
    }
}
```



## MQ

### mq的作用

- 异步处理
- 实现解耦
- 流量削峰

### MQ与多线程实现异步的区别

- 多线程方式实现异步会消耗cpu资源, 会发生cpu竞争的问题
- MQ方式实现异步解耦是完全解耦, 适合于大型项目可能会影响业务线程执行
- 小的项目可以使用多线程实现异步

### mq如何避免消息堆积的问题

产生背景: 生产者投递消息的速率与消费者消费的速率完全不匹配

前者大于后者, 导致消息堆积的问题

注意: rabbitmq中如果消息被消费成功, 则消息会被立即删除; 而kafka和rocketmq中消息被消费成功则不会立即删除

解决: A 提高消费者消费的速率 B: 消费者应该以批量的形式获取消息, 减少网络传输的次数

MQ如何保证消息不丢失

- MQ服务器端将消息持久化到硬盘
- 生产者确保消息投递到mq服务器端, 使用消息确认机制
- 消费者必须确认消息消费成功(rabbitmq才会将消息删除, kafka或者rocketmq才会提交offset)

### 生产者投递消息 mq宕机了如何处理

生产者投递消息会将msg消息内容记录下来, 若发生消息投递失败, 可以根据该日志记录实现补偿机制(获取到msg日志消息内容实现重试)

### mq如何保证消息顺序一致性

产生背景: mq服务器集群或者mq采用分区模型架构存放消息, 每个分区对于一个消费者消费消息

解决: 消息一定要投递到同一个mq、同一个分区模型、最终被同一个消费者消费

核心原理: 设定相同的消息key, 根据相同的消息key, 计算hash, 存放在同一个分区中

大多数的项目是不需要保证mq消息顺序一致性的问题, 只有在一些特定的场景中可能会需要; 比如MySQL和Redis实现异步同步数据

若保证了消息的顺序一致性, 则会降低消费者消费的速率

### mq如何保证消息幂等性

- 消费者获取消息, 如果消费消息失败, mq服务器则会间隔的形式实现重试策略
- 重试过程中, 需要保证业务幂等性问题, 保证业务不能够重复执行
- 可以通过全局的消息id, 提前查询, 如果该业务逻辑已经执行过, 则不会重复执行
- 也需要在db层保证幂等性问题, 唯一主键约束、乐观锁等

### MySQL与Redis如何保证数据一致性

产生背景:

在分布式领域中同步数据 很难保证强一致性策略 都是采用最终一致性思想 短暂的数据延迟是允许的 但是最终数据必须要保证一致性

- 直接删除Redis缓存 (延迟双删策略 适合小项目)
- 基于MQ异步同步更新
- 基于canal框架订阅binlog同步

### 基于canal框架订阅binlog同步

- canal服务器伪装成mysql从节点订阅mysql主节点的binlog文件
- 当mysql主节点binlog文件发生变化时 则会给canal服务器端
- canal服务器将binlog文件转化为json的格式给MQ
- 消费者订阅MQ消息实现以异步的形式将数据同步到redis

## 线程

Java从1.1开始就不再采用用户级线程

### java中线程的状态

- NEW
- RUNNABLE
- WAITING
- TIME_WAITING
- BLOCKED
- TERMINATED

Thread.join() : WAITING

Thread.sleep() : TIME_WAITING

网络请求: BLOCKED

![a524e12c79382f8eacd0d84b6babbc2b](http://cdn.qingweico.cn/blog/a524e12c79382f8eacd0d84b6babbc2b.png)

临界区(critical section): 发生竞争的区域(访问共享资源)

### 解决竞争的办法

- 减少竞争
- 实现原子操作
- TAS指令: 可以看作是cas的一个特例(只支持0到1的更新)
- 互斥

### 什么是同步

- 执行同步
- 数据同步

### synchronized和ReentrantLock的区别

- Non-Blocking的算法(tryLock) + 支持timeout
- 后者使用更灵活
- ReentrantLock提供中断能力(lock.lockInterruptibly())
- synchronized经过性能优化: 早期Java没有轻量级锁的设计

### synchronized关键字的设计

> 一种不完全的实现方案

```java

enter(&lcok) {
    while(!cas(&lock, 0, 1)) {
        // 休眠
    }
}
leave(&lock) {
    lock = 0;
}
enter(&lock);
// 临界区
leave(&lock);
```

对休眠的三种理解: 

- 休眠少量的CPU周期(自旋锁)
- 定时休眠 (Thread.sleep)
- 信号休眠  信号唤醒(wait/notify)

> 改版

- 尝试获取锁 >> 自旋少量次数获取锁 ? 得到锁 : 休眠  >> (唤醒) >> 尝试获取锁
- 优势: 提高在竞争较少时的性能
- 怎么实现唤醒一个线程的功能(操作系统API修改线程的状态以及JVM知道那些线程在休眠)

synchronized需要实现的功能

- 需要实现加锁和解锁的逻辑
- 需要自旋锁到休眠的升级逻辑
- API设计:每个对象都可以上锁
- 线程可以在竞争不到资源时休眠
- 释放资源时唤醒休眠线程

> monitor

每个对象都关联一个monitor

偏向锁、轻量级锁以及重量级锁

### AQS简介

java同步器整体架构

![fc66d0fafe49a6ba9cccae041cc9b286](https://cdn.qingweico.cn/blog/fc66d0fafe49a6ba9cccae041cc9b286.png)

Java提供的同步器开发框架

作用: 将用户和真正意义上的底层隔离: 用户实现同步控制算法时不再需要使用JVM提供的最底层的API 

AQS区别于synchronized(built-in or intrinsic lock) Java提供的另一个实现同步的体系

![d609a8ff2bc13468e341259d43aa70aa](http://cdn.qingweico.cn/blog/d609a8ff2bc13468e341259d43aa70aa.png)

AQS的功能特性

- 基于Java实现
- 提供同步原语和高性能生产消费者结构
- 提供Non-Blocking能力(cas和tryLock)
- 提供定时能力
- 提供中断能力
- 提供线程间协作(Condition)
- 提供扩展数据结构的能力
- 内部提供整数状态
- 封装cas操作(acquire和release都是基于cas状态)以及内部封装高性能CLH队列(cas失败自动进入队列 条件等待自动进入队列)

信号量(Semaphore)的实现

```java
queue = {};
int state = 3;



int localState = state;
do {
    localState = state;
    if(localState == 0) {
        queue.add(Thread.currentThread());
        LockSupport.park();
    }
    
}while(!Unsafe.cas(&state, localState, localState - 1))
// 临界区
while(!Unsafe.cas(&state, state, state + 1)) {
    LockSupport.unpark(queue.remove());
}
```

spin_lock并不是cas loop

但是cas_loop是spin_loop

CLH的解决方案:

尾部插入 头部删除

避免了双向链表且单链表性能好

说出6个Java的同步器

- ReentrantLock
- Semaphore
- CyclicBarrier
- CountDownLatch
- Phaser
- Exchanger

### Reentrant与synchronized的相似点

- 临界区保护 提供加锁和解锁的能力
- 都可重入
- 都提供线程之间的协作
- 提供锁的升级逻辑
  - monitor: 偏向锁 -> 轻量级锁 -> 重量级锁
  - AQS: cas -> 休眠-> 排队竞争
- 都提供等待队列
  - monitor: EnterList WaitSet
  - AQS: CLH队列

### Reentrant与synchronized的区别

- 基于AQS vs 基于monitor
- Java生态 vs 非Java生态
- 相应线程中断(InterruptedException) vs 不响应
- 提供tryLock vs 不提供
- 跨block vs 单block
- 可配置公平性 vs 不可配置

![img](https://cdn.qingweico.cn/blog/412d294ff5535bbcddc0d979b2a339e6102264.png)

### ReentrantLock 中公平锁和非公平锁的获取流程

公平锁:

- 当前线程获取锁的状态
- 判断锁的状态
- 若锁为自由状态(0), 判断是否需要入队; 若是, 则addwaiter进行入队操作; 若为否, 则cas加锁成功返回true,  cas加锁失败则进行入队操作
- 若锁为非自由状态(不是0), 判断是否重入; 若不是重入, 加锁失败, 则进行入队操作,  若是重入则直接获取锁成功; 入队后判断是否需要进行自旋获取锁; 若是, 则开始尝试自旋获取锁, 否则阻塞;

非公平锁: 

- 首先进行cas加锁, 若成功获取锁则设置持有锁的线程为当前线程, 返回true, 否则判断是否重入; 若不是重入, 加锁失败, 返回false; 若是重入则获取锁成功, 返回true
- 若cas加锁失败, 则获取当前锁的状态; 若锁为自由状态, 尝试cas加锁; 若成功获取锁则返回true; 否者获取锁失败, 返回false
- 同公平锁

### Semaphore

信号量的作用是什么: 控制并发量

实现场景: 可以实现生产者消费者

为什么ArrayBlockingQueue不用Semaphore实现: 因为条件变量性能更好

### CyclicBarrier

 CyclicBarrier解决了什么问题:

- 多个线程协作(通信)处理任务的问题: 实现了一套协作机制(循环 + 屏障)

CountDownLatch也是解决了同样的问题, 不过只是一次CyclicBarrier周期

为什么不用ReentrantLock + 条件变量? 

因为CyclicBarrier的场景具有通用性 抽象成数据结构更有价值 比如处理大量的数据和任务

### Phaser

### Exchanger

 用于线程之间交换数据

场景: 利用Exchanger在生产者和消费者之间交换数据

解决了什么问题: 线程间交换数据(交换是一个高效的操作) 但是只能用于两个线程之间

总结

同步器解决了什么问题

- 同步
- 协作模式

## 并发

并发三要素

- 原子性 (线程切换带来的原子性问题)
- 有序性 (编译优化带来的有序性问题)
- 可见性 (缓存导致的可见性问题)

### 内存一致性

绝对意义上的并行并不存在

绝对意义上的并行: 指令A和指令B绝对意义上同时执行并且同时产生结果

不存在同一个时刻某个变量有多种状态(比如两个写内存的指令 不可能写完之后内存处于两种不同的状态)

- 线性一致: 任何时刻都一致
  - Sequential Consistency
  - 单线程环境中永远线性一致
- 弱一致性: 部分时刻一致 (partial) 
  - Weak Consistency
  - 需要同步原语(primitives): 锁、信号量 happens-before原则、volatile 若不使用原语工具 Java是弱一致性
- 没有一致性

内存不一致的原因(相对于不同的观察者线程来说是不一致的):

- 分级缓存策略
- 指令重排

### volatile关键字

- 确保语义上对变量的读、写操作顺序被观察到
  - 对volatile变量的读、写不会被重排到对它后续的读写之后(阻止指令重排)
  - 保证写入的值可以马上同步到CPU缓存中(写入后要求CPU马上刷新缓存等价于synchronized的monitorexit的作用)
  - 保证读取到最新版本的数据(读L3、主存甚至使用内存屏障 不同的CPU架构会有不同的实现方式)
- 如果逻辑上变量的写在读之前发生 那么确保观察到的结果 写也在读之前发生
  - 即happens-before原则
  - 确保有序性和可见性

符合happens-before规则

- 单线程规则: 单线程对内存的访问符合happens-before规则
- Monitor规则: synchronized对锁的释放happens-before对锁的获取
- volatile规则: volatile 变量的操作happens-before对它的后续操作且周围的指令不会重排序
- AtomicReference
- Thread-Start规则: start()调用前的操作happens-before线程内的程序
- Thread.join()规则: 线程的最后一条指令happens-before join后的第一条指令
- happens-before具有传递性 若A happens-before B、B happens-before  C 则A happens-before C

- happens-before不是时间关系; happens-before是发生顺序和观察到的结果关系

- A happens-before B 指的是若A在B之前发生 那么A带来的变化在B可以观察到
- happens-before是partial ordering(参考AtomicReference) 重要的顺序保证 其他仍然可以重排

并发编程三要素

- 原子性
- 有序性
- 可见性

### 解释一下volatile?

- volatile变量读写是会增加内存屏障
- volatile变量读写时会禁用局部指令重排
- 保证对volatile的操作happens-before另一个操作

读屏障就是在读取volatile变量之前增加一条将变量内容从内存读入CPU缓存的指令

写屏障就是在volatile变量写入之后 将变量的值从CPU缓存写入内存中

指令重排是一种CPU策略 通过交换指令执行顺序获得最佳性能

### 阻塞队列

延迟队列

作用: 控制流速

为什么要控制: 大量任务来领 系统负载超过临界点

- 线程切换频繁
-  虚拟内存频繁交换
- I/O资源竞争

方案:

- 延迟队列
- 支持反向压力: 阻塞任务的提交者
- 访问拒绝: 拒绝任务的提交者

![725a3db5114d95675f2098c12dc331c3316963](https://cdn.qingweico.cn/blog/725a3db5114d95675f2098c12dc331c3316963.png)

![img](https://cdn.qingweico.cn/blog/a20efe788caf4f07a4ad027639c80b1d.png)

## 锁

什么是悲观锁

站在mysql的角度分析: 当多个线程对同一行数据实现修改的时候 最后只有一个线程才能修改成功 若某个线程获取到行锁 则其他线程不能够对该数据做任何修改操作 切实阻塞状态

站在java的角度分析: 如果没有获取到锁 则会阻塞等待 后期唤醒锁的成本就会非常高

什么是乐观锁

乐观锁比较乐观 通过预期值或者版本号比较 如果不一致的情况则通过循环控制修改 当前线程不会被阻塞  效率比较高 但是乐观锁比较消耗CPU的资源

锁的重入性: 在同一个线程中锁可以不断传递, 可以直接获取

锁粗化就是将多个同步块的数量减少, 并将单个同步块的作用范围扩大 本质上就是将多次上锁、解锁的请求合并为同一次请求

锁消除就是虚拟机编译器在运行时检测到了共享数据没有锁的竞争, 从而将这些锁进行消除

用Mark Word中最低的三位代表锁状态, 其中1位是偏向锁位, 两位是普通锁位

轻量级锁是 JDK 6时加入的新型锁机制, 轻量级锁并不是用来代替重量级锁的, 它设计的初衷是在没有多线程竞争的前提下, 减少传统的重量级锁使用操作系统互斥量产生的性能消耗

轻量级锁的加锁过程: 在代码即将进入同步块的时候, 如果此同步对象没有被锁定(锁标志位为01状态), 虚拟机首先将在当前线程的栈帧中建立一个名为锁记录(Lock Record)的空间, 用于储存锁对象目前的Mark Word的拷贝, 然后虚拟机使用CAS操作尝试把对象的Mark Word 更新为指向Lock Record 的指针, 如果这个更新成功了, 即代表这个线程拥有了这个线程的锁, 并且Mark Word 的锁标志将转变为00 , 表示此对象处于轻量级锁定状态

自旋锁在 JDK1.4.2 中引入, 使用 -XX:+UseSpinning 来开启; JDK 6 中变为默认开启, 并且引入了自适应的自旋锁

竞争加剧: 有线程超过10次自旋, -XX:PreBlockSpin, 或者自旋线程数超过CPU核数的一半, 1.6之后, 加入自适应自旋 Adapative Self Spinning , JVM自己控制

自适应自旋锁意味着自旋的时间(次数)不再固定, 而是由前一次在同一个锁上的自旋时间及锁的拥有者的状态来决定; 如果在同一个锁对象上, 自旋等待刚刚成功获得过锁, 并且持有锁的线程正在运行中, 那么虚拟机就会认为这次自旋也是很有可能再次成功, 进而它将允许自旋等待持续相对更长的时间; 如果对于某个锁, 自旋很少成功获得过, 那在以后尝试获取这个锁时将可能省略掉自旋过程, 直接阻塞线程, 避免浪费处理器资源

偏向锁由于有锁撤销的过程revoke, 会消耗系统资源, 所以, 在锁争用特别激烈的时候, 用偏向锁未必效率高, 还不如直接使用轻量级锁

偏向锁: 也是 JDK 6中引入的一项锁优化措施, 它的目的是消除数据在无竞争情况下的同步原语, 进一步提高程序的运行性能

默认情况 偏向锁有个时延,  默认是4秒

```java
// 设定时延参数
-XX:BiasedLockingStartupDelay=0
```

上偏向锁，指的就是, 把Mark Word的线程ID改为自己线程ID的过程

批量重偏向或者批量撤销(锁的降级方式: 只有在极端情况下出现, 一般不会出现)

JDK11, 打开就是偏向锁, 而JDK8默认对象头是无锁

![Snipaste_2021-01-29_21-54-41](https://cdn.qingweico.cn/blog/Snipaste_2021-01-29_21-54-41.png) 

锁升级过程

![84f4d5dd908788fec0940431b4b912f0](https://cdn.qingweico.cn/84f4d5dd908788fec0940431b4b912f0.webp)

## 无锁编程

### Lock-Free

定义: 线程之间互相隔离  一个线程的延迟、阻塞、故障不会影响其他线程 同一时刻至少有一个线程可以进步

场景: 

- CLH 队列 >> 线程通过cas竞争加入队列
- SynchronousQueue cas竞争实现transfer操作(双向栈后者双向队列 )

自旋锁(spinlock)不是LockFree

```java
spin_lock();
i++;
spin_unlock();
```

### Obstruction Free

- 线程间隔离(一个线程的延迟不影响其他线程的进步)
- 不要求一定有线程进步
- 线程最终可以进步

Lock Free是在Obstruction Free的基础上保证至少有一个线程进步

### Wait Free

- 在Lock Free的基础上保证所有线程同时进步
- 比Lock Free有更强的限制条件
- 场景: CopyOnWrite的读线程
  - 先拷贝o1 >> o2
  - 更新o2
  - 替换指向o1的引用

### Lockless(不用锁) 和 LockFree(无锁)的区别

不用锁的算法通常都是Lockless 线程之间可能回互相影响 例如阻塞队列(本质还是排队)

## ThreadLocal

哪些地方用到: 

- Spring事务模板
- Spring MVC获取HttpRequest
- AOP LCN分布式事务、分布式服务追踪框架源码

ThreadLocal与synchronized区别

 都可以实现在多线程环境中保证线程安全

不同

- 前者在每个线程中都有自己独立的局部变量; 空间换时间, 变量之间相互隔离, 相对来说效率比synchronized高
- 后者当多个线程竞争到同一个资源的时候, 最终只能有一个线程访问, 采用时间换空间的方式, 保证线程的安全问题

### 谈谈你对ThreadLocal的理解

ThreadLocal提供了线程本地变量, 它可以保证访问到的变量属于当前线程, 每个线程都保存一个变量副本, 每个线程的变量都不同; ThreadLocal相当于提供了一种线程隔离, 将变量与线程相绑定, 实现传递数据, 线程隔离

ThreadLocal底层实现原理

- 每个线程中都有自立独立的ThreadLocalMap对象
- 如果当前线程对应的ThreadLocalMap对象为空的情况下, 则创建该ThreadLocalMap对象, 并且赋值键值对
- key为当前线程所创建的ThreadLocal对象, value就是object变量值

### 为什么线程缓存的是ThreadLocalMap对象

因为每个ThreadLocal对象只能缓存一个变量

而每个线程中可能会缓存有多个变量即多个ThreadLocal对象  而ThreadLocalMap可以存放n多个不同的ThreadLocal对象

强、软、弱、虚引用的区别

### 强引用 软引用 弱引用 虚引用

强引用: 当内存不足时 JVM开始进行GC 对于强引用对象 就算是出现了OOM也不会对该引用斤进行回收

软引用: 当系统内存充足的时候不会被回收 而当系统内存不足时就会被回收 软引用通常用在对内存敏感的程序中 比如高速缓存就用到软引用 内存够用时就保留 不够时就回收

弱引用: 弱引用需要用到java.lang.ref.WeakReference来来实现 它比软引用生命周期更短 对于只有弱引用的对象来说 只要有GC 不管JVM的内存空间够不够用都会回收该对象占用的空间

虚引用: 虚引用需要java.lang.ref.PhantomReference类来实现 虚引用就是形同虚设 与其他几种引用不同 虚引用并不会决定对象的生命周期

### ThreadLocal为何会引发内存泄漏

内存泄漏: 申请了内存 但是内存得不到释放

内存泄漏溢出:  申请内存时 发现申请内存不足 就会报错

原因:每个线程中都有自己独立的ThreadLocalMap对象 key为ThreadLocal对象 value是变量的值 

key为ThreadLocal作为Entry对象的key 是***弱引用*** 而当ThreadLocal指向null的时候 Entry对象中的key变为空 该对象一直无法被GC回收 一直占用系统内存 有可能发生内存泄漏的问题

### 如何防御ThreadLocal内存泄漏的问题

- 调用remove方法将不用的数据移出

ThreadLocalMap与当前线程的生命周期一致

尽量不要使用全局的ThreadLocal

## 数据库

事务范围内没有上锁 即事务可以并发的启动

select for update触发了行锁

脏读(Dirty Read): 看到了未提交的变更

幻读(Phantom Read) 看到不存在的数据(未提交新创建的行或者未提交已删除的行)

MVCC: multiple version concurrency control(多版本并发控制)

数据库一致性即事务执行前后关系不被破坏

锁

- 共享锁  允许读读 不可以读写或者写写
- 互斥锁

select * from table for update 触发表级的共享锁

select * from table where id = 1 for update 触发行级的共享锁

数据库的持久性: 没有100%的持久性

### HikariCP

Spring Boot 2.0 默认使用HikariCP

Spring Boot 1.0 默认使用 Tomcay 连接池 需要移除 tomcat-jdbc 依赖

为什么HikariCP这么快

- 字节码几级别优化(很多方法通过JavaAssist生成)
- 用 FastStatementList 代替 ArrayList
- 无锁集合 ConcurrentBag
- 代理类的优化(比如使用 invokestatic 代替 invokevirtual)

## 缓存

缓存起源于CPU缓存

缓存是为了加速数据访问的存储

- 降低延迟
- 提高吞吐量

读

- 缓存穿透: 没有读到数据 读取缓存后的存储 如果在短时间内大量的请求没有命中则会发生缓存击穿
- 命中即缓存条目存在
- 穿透即没有命中

写

- 写入穿透 双写缓存和储存
- 回写
  -  先写缓存
  -  延迟写入储存 比如按时延迟 缓冲延迟(redis中的订单列表) 其他事件触发(volatile)

LRU(Least Recently Used)

LFU(Least Frequent Used)

MRU(Most Recently Used)

多级缓存: 布隆过滤器

## Spring

Spring 的事务抽象

一致的事务模型

- JDBC/Hibernate/MyBatis
- DataSource/JTA

事务抽象的核心接口

- org.springframework.transaction.PlatformTransactionManager
  - org.springframework.jdbc.datasource.DataSourceTransactionManager
  - org.springframework.transaction.jta.JtaTransactionManager
  - ...
- org.springframework.transaction.TransactionDefinition

Spring 事务的传播特性

Spring 事务的隔离级别参考MySQL事务隔离级别



