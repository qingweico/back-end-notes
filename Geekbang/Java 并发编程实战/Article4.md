### Java 内存模型

导致可见性的原因是缓存，导致有序性的原因是编译优化，那解决可见性、有序性最直接的办法就是禁用缓存和编译优化，但是这样问题虽然解决了，我们程序的性能可就堪忧了

合理的方案应该是按需禁用缓存以及编译优化

所以，为了解决可见性和有序性问题，只需要提供给程序员按需禁用缓存和编译优化的方法即可

Java 内存模型规范了 JVM 如何提供按需禁用缓存和编译优化的方法。具体来说，这些方法包括 volatile、synchronized 和 final 三个关键字，以及六项 Happens-Before 规则

### 使用 volatile 的困惑

volatile 关键字并不是 Java 语言的特产，古老的 C 语言里也有，它最原始的意义就是禁用 CPU 缓存

例如，我们声明一个 volatile 变量 volatile int x = 0，它表达的是：告诉编译器，对这个变量的读写，不能使用 CPU 缓存，必须从内存中读取或者写入

```java
class VolatileExample {
  int x = 0;
  volatile boolean v = false;
  public void writer() {
    x = 42;
    v = true;
  }
  public void reader() {
    if (v == true) {
      // 这里x会是多少呢？
    }
  }
}
```

如果在低于 1.5 版本上运行，x 可能是 42，也有可能是 0；如果在 1.5 以上的版本上运行，x 就是等于 42	

在1.5之前 使用volatile修饰只能解决该修饰变量的内存可见性问题 只能保证这个变量的修改可以立即被其他线程看到

Java 内存模型在 1.5 版本对 volatile 语义进行了增强 ： Happens-Before 规则

### Happens-Before 规则

如何理解 Happens-Before 呢？如果望文生义（很多网文也都爱按字面意思翻译成“先行发生”），那就南辕北辙了，Happens-Before 并不是说前面一个操作发生在后续操作的前面，它真正要表达的是：前面一个操作的结果对后续操作是可见的。就像有心灵感应的两个人，虽然远隔千里，一个人心之所想，另一个人都看得到。Happens-Before 规则就是要保证线程之间的这种“心灵感应”。所以比较正式的说法是：Happens-Before 约束了编译器的优化行为，虽允许编译器优化，但是要求编译器优化后一定遵守 Happens-Before 规则

#### 程序的顺序性规则

指在一个线程中，按照程序顺序，前面的操作 Happens-Before 于后续的任意操作

#### volatile 变量规则

指对一个 volatile 变量的写操作， Happens-Before 于后续对这个 volatile 变量的读操作

#### 传递性

如果 A Happens-Before B，且 B Happens-Before C，那么 A Happens-Before C

对于上面代码的解释

“x=42” Happens-Before 写变量 “v=true” ，这是程序的顺序性规则 ；

写变量“v=true” Happens-Before 读变量 “v=true”，这是volatile 变量规则 

再根据这个传递性规则，得到结果：“x=42” Happens-Before 读变量“v=true”

如果线程 B 读到了“v=true”，那么线程 A 设置的“x=42”对线程 B 是可见的。也就是说，线程 B 能看到 “x == 42”

这就是 1.5 版本对 volatile 语义的增强，这个增强意义重大，1.5 版本的并发工具包就是靠 volatile 语义来搞定可见性的

#### 管程中锁的规则

指对一个锁的解锁 Happens-Before 于后续对这个锁的加锁

管程是一种通用的同步原语，在 Java 中指的就是 synchronized，synchronized 是 Java 里对管程的实现

#### 线程 start() 规则

指主线程 A 启动子线程 B 后，子线程 B 能够看到主线程在启动子线程 B 前的操作

#### 线程 join() 规则

指主线程 A 等待子线程 B 完成（主线程 A 通过调用子线程 B 的 join() 方法实现），当子线程 B 完成后（主线程 A 中 join() 方法返回），主线程能够看到子线程的操作。当然所谓的“看到”，指的是对共享变量的操作

就是说在A线程在等待B线程结束之后 A线程可以看到B线程结束之前期间所作的修改

#### final关键字

final 修饰变量时，初衷是告诉编译器：这个变量生而不变，可以可劲儿优化。Java 编译器在 1.5 以前的版本的确优化得很努力，以至于都优化错了

利用双重检查方法创建单例，构造函数的错误重排导致线程可能看到 final 变量的值会变化

在 1.5 以后 Java 内存模型对 final 类型变量的重排进行了约束。现在只要我们提供正确构造函数没有“逸出”，就不会出问题了

逸出 指的是对封装性的破坏。比如对一个对象的操作，通过将这个对象的this赋值给一个外部全局变量，使得这个全局变量可以绕过对象的封装接口直接访问对象中的成员，这就是逸出	

---

happens-Before 的语义是一种因果关系。在现实世界里，如果 A 事件是导致 B 事件的起因，那么 A 事件一定是先于（Happens-Before）B 事件发生的，这个就是 Happens-Before 语义的现实理解

在 Java 语言里面，Happens-Before 的语义本质上是一种可见性，A Happens-Before B 意味着 A 事件对 B 事件来说是可见的，无论 A 事件和 B 事件是否发生在同一个线程里。例如 A 事件发生在线程 1 上，B 事件发生在线程 2 上，Happens-Before 规则保证线程 2 上也能看到 A 事件的发生

Java 内存模型主要分为两部分，一部分面向你我这种编写并发程序的应用开发人员，另一部分是面向 JVM 的实现人员的

[JSR 133 (Java Memory Model) FAQ](http://www.cs.umd.edu/~pugh/java/memoryModel/jsr-133-faq.html)