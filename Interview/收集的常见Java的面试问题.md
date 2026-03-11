[TOC]

## Java 基础

### JDK和JRE的区别

JDK是整个Java的核心, 包括了Java运行环境JRE, 一堆Java工具和Java基础的类库; 通过JDK开发人员将源码文件编译成字节码文件; JRE则是Java运行的环境, 不含开发环境, 即没有编译器和调试器, 将class文件加载到内存中准备运行

### final关键字  抽象类可以被final修饰吗

- 用来修饰数据, 包括成员变量和局部变量, 该变量只能被赋值一次且它的值不能被修改, 而对于成员变量必须在声明时赋值, 最迟要在构造方法中赋值 
- 修饰方法, 表示该方法无法被重写 
- 修饰类, 表示该类无法被继承
- 抽象类是用于被继承的, final修饰代表不可修改, 不可继承, 所以不能使用final修饰类

### Java容器

- ArrayList底层数组实现, 封装了常见的增删改查的操作, 并且支持动态扩展, 适合查找多的场合 

- LinkedList是基于链表实现的列表, 适合增删较多的场合 

- TreeSet是基于二叉排序树(**红黑树**)实现的, TreeSet里面最典型的就是用到了两种排序方式

  - 基于元素对象自身的实现的Comparable接口的自然排序 

  - 基于更为灵活不与单个元素绑定的Comparator接口的客户化排序(自己在构造的时候传入一个比较器即可)

- HashMap是用来储存键值对的映射关系, 底层使用数组+链表+红黑树实现的 

- HashSet是基于HashMap实现的, 只不过将值固定为一个固定的值(null) 

- LinkedHashMap支持按照插入顺序排序 

- PriorityQueue优先级队列 一个基于优先级堆(默认最小堆)的无界优先级队列  

###  线程安全体现在三个方面

- 原子性: 提供互斥访问, 同一时刻只能有一个线程对数据进行修改(atomic synchronized) 
- 可见性: 一个线程对内存的修改可以及时的被其他线程看到(volatile synchronized) 
- 有序性: 一个线程观察其他线程中的指令执行顺序, 由于指令重排序, 该观察结果一般杂乱无序(happens-before) 

### Java如何保证原子性

锁和同步

常用的保证Java操作原子性的工具是锁(lock)和同步方法(或者同步代码块), 使用锁, 可以保证同一时间只有一个线程能拿到锁, 也就保证了同一时间只有一个线程能执行申请锁和释放锁之间的代码

- 使用非静态同步方法时, 锁住的是当前实例 
- 使用静态同步方法时, 锁住的是该类的Class对象 
- 使用代码块时, 锁住的是synchronized关键字后面括号的内容 

无论是使用锁还是使用synchronized关键字, 本质都是一样的, 通过锁来实现资源的排它性, 从而实现目标代码段同一时间只能被一个线程执行, 进而保证目标代码段的原子性, 这是一种以牺牲性能为代价的方法

### Java如何保证可见性

Java提供volatile关键字来保证可见性

 由于JVM是基于共享内存实现线程通讯的, 所以会存在缓存一致性的问题; 当使用volatile修饰某个变量时, 它会保证对该变量的修改会立即被更新到内存中, 并且将其他缓存中对该变量的缓存设置为无效, 因此其他线程需要读取该值时必须从主内存中读取, 从而得到最新的值

### Java如何保证顺序性

编译器和处理器对指令进行重新排序时, 会保证重新排序后的执行结果和代码顺序执行的结果一致, 所以重新排序过程并不会影响到单线程程序的执行, 却可能影响多线程程序并发执行的正确性; Java中可通过volatile在一定程度上保证顺序性, 另外还可以通过synchronized和锁来保证顺序性; synchronized和锁保证顺序的原理和原子性一样, 都是*保证同一时间只会有一个线程执行目标代码段来实现的*; 除了从应用层面保证目标代码段执行的顺序性之外, JVM还通过被称为 ***happens-before***原则隐式的保证顺序; 两个的操作顺序只要可以通过happens-before推导出来, 则JVM会保证其顺序性, 反之JVM对其顺序不做任何保证, 可以对其进行必要的重新排序以获取高效率

### 有没有其他方法保证线程安全？

有, 尽可能避免引用非线程安全的条件——共享变量; 如果能从设计上避免共享变量的使用, 即可避免非线程安全的发生, 也就无需通过锁或者synchronized以及volatile解决原子性、可见性和顺序性的问题; 另外, ***不可变对象***也可以保证线程安全, 可以使用final修饰的对象保证线程安全, 由于final修饰的引用型变量(***除了String外***)不可变是指引用不可变, 但其指向的对象内容是可变的, 所以此类必须安全发布, 即不能对外提供可以修改final对象的接口

### String、StringBuffer、StringBuilder的区别

StringBuffer线程安全 但是执行速度慢(相对于StringBuilder而言)
StringBuilder线程不安全 但是执行速度快(相对于StringBuffer而言)    

### 执行效率StringBuilder>StringBuffer>String  

单线程操作字符串缓冲区时操作大量数据使用StringBuilder 

多线程下字符串缓冲区时操作大量数据使用StringBuffer

StringBuffer内部被synchronized关键字修饰,执行效率会慢于StringBuilder

### 常见的加密算法有哪些？并说明一种

```properties
常见的加密算法有DES AES RSA Base64 MD5
```

### 显示(强制)类型转换和隐式类型转换

八种数据类型之间除了boolean之外的其中类型都可以相互转换

任何浮点类型不管占用多大的字节都要比整型容量大

char和short虽然所占的字节数一样,因为char类型没有负数,可以取得比short更大的整数

当byte,char,short没有超过各自的字面值大小时可以直接赋值

当byte,char,short混合运算时都会转换为int类型再做运算

多种数据类型混合运算时,先转换为最大的那种类型再做运算

[数据类型强制转换]

将大容量的数据类型转换为小容量的数据类型称为强制转换,会发生精度丢失

```java
public class Test {
    public static void main(String[] args) {
        byte b = (byte) 128;              // 128默认为int型 0000 0000 1000 0000
        System.out.println(b); //-128     // 强制转化为byte型为 1000 0000(补码)
     }                                    // 原码还是1000 0000即-128
}                                         // 1000 0000(-128)
```

**submit方法**:
```java
int a = -129;     
System.out.println((byte)a); //127
/*-129的原码是1000 0000 0000 0000 0000 0000 1000 0001
在计算机中保存的是补码所以是
             1111 1111 1111 1111 1111 1111 0111 1111
强制转换为byte即0111 1111(补码) 因为0111 1111一看最高位是0所以为正数即为127
*/
```

正数的反码和补码是其本身

负数的反码是除符号位全部取反,补码为反码加一

在计算机系统中,数值一律采用补码的形式储存

计算(byte)数值的原码：8  直接计算 0000 1000

计算负数的原码：

- 先计算补码, 该负数对应的正数的原码所有位都取反包括最高位,再加一,得到该负数补码
- 然后补码减一, 除了最高位全部取反即可

```java
public class Test {
    public static void main(String[] args) {
        byte a = -68;
        if(a < 0){
           a += 256;           /*byte类型的256为0 等价于    a = (byte) (a  + 256) +=操作符 会进行自动强制转换
                               byte与int混合运算时byte类型的a会转换为int类型的不会发生精度丢失即还是-68加上256即为                                  188,此时188为int类型 原码为00000000 00000000 00000000 1011 1100
                               强转为byte类型为1011 1100(补码)
                                             1011 1011(反码)
                                             1100 0100(原码) 即为-68*/
        }
        System.out.println(a);  //-68
    }
}
```

[隐式转换]

小容量的数据类型转换为大容量的数据类型称为自动类型转换也叫隐式转换

byte < short(char) < int < long < float < double

### 静态变量,静态方法和静态代码块以及构造方法的执行顺序

静态变量最先执行,然后执行静态代码块,接着执行静态方法,最后执行构造方法

### JDK1.7和JDK1.8有哪些区别

编程语言

- Lambda表达式
- 方法引用
- 默认方法
- 可重复注解
- 类型注解: 在 Java8之前 注解只能出现在声明的地方所使用 比如类、方法、属性; 在Java8里面, 注解可以应用在任何地方
- 方法参数反射

集合

- Stream API
- HashMap优化

JVM

- 永久代移出

新的日期时间 API

并行(parallel)数组: Arrays::parallelSort([])

Base64编码

### String类常用方法有哪些

**推荐做法**: 掌握常用方法分类, 根据场景选择合适方法

**常用方法分类**:

1. **字符串创建和转换**
   - `String.valueOf()`: 将其他类型转为字符串(推荐, 避免NPE)
   - `new String(char[])`: 字符数组转字符串
   - `toCharArray()`: 字符串转字符数组
   - `getBytes()`: 字符串转字节数组

2. **字符串比较**
   - `equals(Object obj)`: 内容比较(推荐, 避免空指针)
   - `equalsIgnoreCase(String)`: 忽略大小写比较
   - `compareTo(String)`: 字典序比较, 返回int
   - `contentEquals(CharSequence)`: 与CharSequence比较

3. **字符串查找**
   - `indexOf(String/char)`: 查找首次出现位置, 未找到返回-1
   - `lastIndexOf(String/char)`: 查找最后一次出现位置
   - `contains(CharSequence)`: 是否包含子串(推荐, 语义清晰)
   - `startsWith(String)`: 是否以指定前缀开始
   - `endsWith(String)`: 是否以指定后缀结束

4. **字符串截取和分割**
   - `substring(int beginIndex)`: 从指定位置截取到末尾
   - `substring(int beginIndex, int endIndex)`: 截取指定范围(左闭右开)
   - `split(String regex)`: 按正则分割(注意特殊字符需转义)
   - `split(String regex, int limit)`: 限制分割次数

5. **字符串替换**
   - `replace(char oldChar, char newChar)`: 替换所有字符
   - `replace(CharSequence target, CharSequence replacement)`: 替换所有子串
   - `replaceAll(String regex, String replacement)`: 正则替换
   - `replaceFirst(String regex, String replacement)`: 替换第一个匹配

6. **字符串格式化**
   - `format(String format, Object... args)`: 格式化字符串
   - `join(CharSequence delimiter, CharSequence... elements)`: 连接字符串(Java 8+)

7. **字符串大小写和空白处理**
   - `toLowerCase()`: 转小写
   - `toUpperCase()`: 转大写
   - `trim()`: 去除首尾空白(不包含中间空白)
   - `strip()`: 去除首尾空白(Java 11+, 支持Unicode空白字符)
   - `isBlank()`: 是否为空或仅包含空白(Java 11+)
   - `isEmpty()`: 是否为空字符串

8. **其他常用方法**
   - `length()`: 字符串长度
   - `charAt(int index)`: 获取指定位置字符
   - `concat(String str)`: 连接字符串(不如+操作符常用)
   - `matches(String regex)`: 是否匹配正则表达式

**为什么不推荐其他做法**:
- 避免使用`==`比较字符串内容, 应使用`equals()`
- 避免频繁使用`+`拼接大量字符串, 应使用`StringBuilder`
- `substring()`在Java 7+会创建新对象, 注意内存使用

### Files类常用方法有哪些

**推荐做法**: 使用`java.nio.file.Files`进行文件操作, 比传统IO更简洁高效

**常用方法分类**:

1. **文件读取**
   ```java
   // 读取所有行
   List<String> lines = Files.readAllLines(path, StandardCharsets.UTF_8);
   
   // 读取所有字节
   byte[] bytes = Files.readAllBytes(path);
   
   // 按行流式读取(大文件推荐)
   Stream<String> lines = Files.lines(path, StandardCharsets.UTF_8);
   ```

2. **文件写入**
   ```java
   // 写入字节
   Files.write(path, bytes);
   
   // 写入行
   Files.write(path, lines, StandardCharsets.UTF_8);
   
   // 追加写入
   Files.write(path, content.getBytes(), StandardOpenOption.APPEND);
   ```

3. **文件复制和移动**
   ```java
   // 复制文件
   Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
   
   // 移动文件
   Files.move(source, target, StandardCopyOption.REPLACE_EXISTING);
   ```

4. **文件创建和删除**
   ```java
   // 创建文件
   Files.createFile(path);
   
   // 创建目录
   Files.createDirectories(path); // 创建多级目录
   Files.createDirectory(path);   // 创建单级目录
   
   // 删除文件或目录
   Files.delete(path);
   Files.deleteIfExists(path); // 不存在不抛异常
   ```

5. **文件属性查询**
   ```java
   // 文件是否存在
   boolean exists = Files.exists(path);
   
   // 是否为目录
   boolean isDir = Files.isDirectory(path);
   
   // 是否为普通文件
   boolean isFile = Files.isRegularFile(path);
   
   // 文件大小
   long size = Files.size(path);
   
   // 获取文件属性
   BasicFileAttributes attrs = Files.readAttributes(path, BasicFileAttributes.class);
   ```

6. **文件遍历**
   ```java
   // 遍历目录
   Stream<Path> paths = Files.list(directory);
   
   // 递归遍历
   Stream<Path> paths = Files.walk(directory);
   
   // 查找文件
   Stream<Path> paths = Files.find(directory, maxDepth, 
       (path, attrs) -> attrs.isRegularFile() && path.toString().endsWith(".java"));
   ```

7. **临时文件**
   ```java
   // 创建临时文件
   Path tempFile = Files.createTempFile("prefix", ".suffix");
   
   // 创建临时目录
   Path tempDir = Files.createTempDirectory("prefix");
   ```

**为什么不推荐其他做法**:
- 传统`FileInputStream/FileOutputStream`需要手动关闭资源, `Files`方法自动管理
- `Files`提供更多便捷方法, 代码更简洁
- 大文件使用`Files.lines()`流式处理, 避免内存溢出

### Iterator 怎么使用; 有哪些特点

**推荐做法**: 使用Iterator遍历集合, 支持在遍历过程中安全删除元素

**基本使用**:
```java
// 标准遍历方式
List<String> list = Arrays.asList("a", "b", "c");
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String element = iterator.next();
    System.out.println(element);
}

// 增强for循环底层也是Iterator(推荐, 更简洁)
for (String element : list) {
    System.out.println(element);
}

// Java 8+ Stream方式(推荐, 函数式编程)
list.forEach(System.out::println);
```

**Iterator特点**:
1. **统一访问接口**: 所有Collection都实现Iterator, 提供统一遍历方式
2. **支持安全删除**: 遍历过程中可以调用`remove()`删除当前元素
3. **fail-fast机制**: 遍历时如果集合被其他线程修改, 会抛出`ConcurrentModificationException`
4. **单向遍历**: 只能向前遍历, 不能后退

**安全删除示例**:
```java
List<String> list = new ArrayList<>(Arrays.asList("a", "b", "c"));
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String element = iterator.next();
    if ("b".equals(element)) {
        iterator.remove(); // 安全删除, 不会抛异常
    }
}
```

**为什么不推荐其他做法**:
- 避免在增强for循环中直接删除元素, 会抛`ConcurrentModificationException`
- 避免使用传统for循环通过索引删除, 容易出错且效率低

### NoClassDefFoundError和ClassNotFoundException的区别

**推荐做法**: 理解两者区别, 快速定位问题根源

**区别对比**:

| 特性 | ClassNotFoundException | NoClassDefFoundError |
|------|----------------------|---------------------|
| **异常类型** | 受检异常(Checked Exception) | 非受检异常(Unchecked Exception) |
| **发生时机** | 编译时或运行时动态加载类时 | 运行时JVM找不到类定义 |
| **常见原因** | ClassLoader找不到类文件 | 类在编译时存在, 运行时缺失 |
| **典型场景** | `Class.forName()`, `ClassLoader.loadClass()` | 类依赖缺失, 类文件损坏 |
| **处理方式** | 必须捕获或声明throws | 通常表示配置问题 |

**ClassNotFoundException示例**:
```java
try {
    Class<?> clazz = Class.forName("com.example.NonExistentClass");
} catch (ClassNotFoundException e) {
    // 类路径中找不到该类
    e.printStackTrace();
}
```

**NoClassDefFoundError示例**:
```java
// 编译时: A类依赖B类, 编译通过
// 运行时: B类的class文件缺失或不在classpath中
// 结果: 抛出NoClassDefFoundError
public class A {
    private B b = new B(); // 运行时找不到B类定义
}
```

**排查建议**:
- `ClassNotFoundException`: 检查classpath配置, 确认jar包是否包含
- `NoClassDefFoundError`: 检查依赖jar包是否完整, 类文件是否损坏

### ArrayList的扩容是怎么实现; 扩容过程中能不能继续进行 add 操作

**推荐做法**: 理解扩容机制, 合理设置初始容量避免频繁扩容

**扩容实现原理**:

1. **默认容量**: 无参构造创建ArrayList时, 初始容量为0, 首次add时扩容为10
2. **扩容触发**: 当`size >= elementData.length`时触发扩容
3. **扩容算法**: `newCapacity = oldCapacity + (oldCapacity >> 1)`, 即扩容1.5倍
4. **最大容量**: `Integer.MAX_VALUE - 8`(某些VM需要数组头信息)

**源码分析**:
```java
// JDK 8+ ArrayList扩容核心代码
private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1); // 1.5倍
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

**扩容过程中add操作**:
- **可以继续add**: 扩容是同步操作, 扩容完成后立即可以add
- **线程安全**: ArrayList非线程安全, 多线程环境下扩容和add可能出问题
- **性能影响**: 扩容需要数组拷贝, 频繁扩容影响性能

**推荐做法**:
```java
// 预估容量, 避免频繁扩容
List<String> list = new ArrayList<>(100); // 指定初始容量

// 或使用Guava Lists
List<String> list = Lists.newArrayListWithExpectedSize(100);
```

**为什么不推荐其他做法**:
- 避免无参构造后频繁add大量元素, 应预估容量
- 避免在多线程环境下使用ArrayList, 应使用`CopyOnWriteArrayList`或`Collections.synchronizedList()`

### Java序列化了解吗; 什么是序列化; 有哪些方式; 如果自定义的话怎么做

**推荐做法**: 理解序列化机制, 根据场景选择合适的序列化方式

**什么是序列化**:
序列化是将Java对象转换为字节流的过程, 反序列化是将字节流恢复为Java对象的过程

**序列化方式**:

1. **Java原生序列化(Serializable)**
   ```java
   // 实现Serializable接口
   public class User implements Serializable {
       private static final long serialVersionUID = 1L;
       private String name;
       private transient int age; // transient字段不序列化
       
       // 自定义序列化
       private void writeObject(ObjectOutputStream out) throws IOException {
           out.defaultWriteObject();
           out.writeInt(age); // 手动序列化transient字段
       }
       
       private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
           in.defaultReadObject();
           age = in.readInt(); // 手动反序列化
       }
   }
   ```

2. **Externalizable接口(完全自定义)**
   ```java
   public class User implements Externalizable {
       private String name;
       
       @Override
       public void writeExternal(ObjectOutput out) throws IOException {
           out.writeUTF(name);
       }
       
       @Override
       public void readExternal(ObjectInput in) throws IOException {
           name = in.readUTF();
       }
   }
   ```

3. **JSON序列化(推荐, 跨语言)**
   ```java
   // 使用Jackson
   ObjectMapper mapper = new ObjectMapper();
   String json = mapper.writeValueAsString(user);
   User user = mapper.readValue(json, User.class);
   ```

4. **其他序列化框架**
   - **Kryo**: 高性能二进制序列化
   - **Hessian**: 跨语言二进制协议
   - **Protobuf**: Google的高效序列化协议

**自定义序列化最佳实践**:
```java
public class User implements Serializable {
    private static final long serialVersionUID = 1L;
    private String name;
    private transient String password; // 敏感信息不序列化
    
    // 自定义序列化逻辑
    private void writeObject(ObjectOutputStream out) throws IOException {
        out.defaultWriteObject();
        // 可以加密敏感信息后再序列化
    }
    
    private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
        in.defaultReadObject();
        // 解密敏感信息
    }
    
    // 序列化代理模式(推荐, 更安全)
    private Object writeReplace() {
        return new SerializationProxy(this);
    }
    
    private static class SerializationProxy implements Serializable {
        private final String name;
        
        SerializationProxy(User user) {
            this.name = user.name;
        }
        
        private Object readResolve() {
            return new User(name);
        }
    }
}
```

**为什么不推荐其他做法**:
- 避免序列化敏感信息, 使用`transient`或加密
- 避免频繁使用Java原生序列化, 性能较差, 推荐JSON或二进制协议
- 必须定义`serialVersionUID`, 避免版本兼容问题

### 序列化、反序列化的作用

**推荐做法**: 理解序列化应用场景, 合理使用序列化技术

**主要作用**:

1. **对象持久化**: 将对象保存到文件或数据库
   ```java
   // 保存对象到文件
   try (ObjectOutputStream oos = new ObjectOutputStream(
           new FileOutputStream("user.dat"))) {
       oos.writeObject(user);
   }
   ```

2. **网络传输**: 在分布式系统中传输对象
   ```java
   // RPC调用中序列化参数和返回值
   // Spring Cloud, Dubbo等都依赖序列化
   ```

3. **缓存存储**: Redis等缓存系统需要序列化对象
   ```java
   // Redis存储对象
   redisTemplate.opsForValue().set("user", user);
   ```

4. **深拷贝**: 通过序列化实现对象深拷贝
   ```java
   public static <T> T deepCopy(T obj) throws Exception {
       ByteArrayOutputStream baos = new ByteArrayOutputStream();
       ObjectOutputStream oos = new ObjectOutputStream(baos);
       oos.writeObject(obj);
       
       ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
       ObjectInputStream ois = new ObjectInputStream(bais);
       return (T) ois.readObject();
   }
   ```

5. **进程间通信**: 不同JVM进程间传递对象

**注意事项**:
- 序列化会带来性能开销, 谨慎使用
- 序列化后的数据不跨语言, 推荐使用JSON或Protobuf
- 注意版本兼容性, 修改类结构可能导致反序列化失败

### 用Iterator遍历Map

**推荐做法**: 使用EntrySet遍历, 性能最优

**遍历方式对比**:

1. **EntrySet遍历(推荐, 性能最优)**
   ```java
   Map<String, Integer> map = new HashMap<>();
   for (Map.Entry<String, Integer> entry : map.entrySet()) {
       String key = entry.getKey();
       Integer value = entry.getValue();
   }
   ```

2. **KeySet遍历**
   ```java
   for (String key : map.keySet()) {
       Integer value = map.get(key); // 需要二次查找, 性能较差
   }
   ```

3. **Values遍历(只需要值)**
   ```java
   for (Integer value : map.values()) {
       // 只需要值
   }
   ```

4. **Iterator方式**
   ```java
   Iterator<Map.Entry<String, Integer>> iterator = map.entrySet().iterator();
   while (iterator.hasNext()) {
       Map.Entry<String, Integer> entry = iterator.next();
       // 可以在遍历中安全删除
       if (condition) {
           iterator.remove();
       }
   }
   ```

5. **Java 8+ Stream方式(推荐, 函数式)**
   ```java
   map.entrySet().stream()
       .filter(entry -> entry.getValue() > 10)
       .forEach(entry -> System.out.println(entry.getKey()));
   ```

**性能对比**: EntrySet > KeySet > Values(仅遍历值时)

**为什么不推荐其他做法**:
- 避免使用KeySet+get方式遍历, 需要两次查找, 性能差
- 避免在增强for循环中删除元素, 使用Iterator.remove()

### Jason;Kryo;Hessian;ProtoBuf四种序列化算法的特点和差异; 看过怎么实现的吗

**推荐做法**: 根据场景选择合适的序列化框架

**四种序列化框架对比**:

| 特性 | JSON | Kryo | Hessian | Protobuf |
|------|------|------|---------|----------|
| **格式** | 文本(JSON) | 二进制 | 二进制 | 二进制 |
| **跨语言** | ✅ 是 | ❌ 否 | ✅ 是 | ✅ 是 |
| **性能** | 中等 | 很高 | 较高 | 很高 |
| **可读性** | ✅ 好 | ❌ 差 | ❌ 差 | ❌ 差 |
| **体积** | 较大 | 较小 | 中等 | 最小 |
| **使用复杂度** | 低 | 中 | 中 | 中(需定义.proto) |
| **适用场景** | REST API, 日志 | 高性能RPC | 跨语言RPC | 高性能, 跨语言 |

**JSON序列化**:
```java
// 使用Jackson
ObjectMapper mapper = new ObjectMapper();
String json = mapper.writeValueAsString(user);
// 优点: 可读性好, 跨语言, 调试方便
// 缺点: 体积大, 性能一般
```

**Kryo序列化**:
```java
Kryo kryo = new Kryo();
Output output = new Output(new FileOutputStream("file.bin"));
kryo.writeObject(output, user);
output.close();
// 优点: 性能极高, 体积小
// 缺点: 不跨语言, 需要注册类
```

**Hessian序列化**:
```java
HessianOutput out = new HessianOutput(outputStream);
out.writeObject(user);
// 优点: 跨语言, 性能好
// 缺点: 对复杂对象支持有限
```

**Protobuf序列化**:
```protobuf
// user.proto
message User {
    string name = 1;
    int32 age = 2;
}
```
```java
UserProto.User user = UserProto.User.newBuilder()
    .setName("John")
    .setAge(30)
    .build();
byte[] data = user.toByteArray();
// 优点: 性能极高, 体积最小, 跨语言
// 缺点: 需要定义schema, 修改schema需考虑兼容性
```

**实现原理简述**:
- **JSON**: 反射获取对象字段, 转换为JSON字符串
- **Kryo**: 直接操作字节码, 避免反射开销, 使用变长编码压缩
- **Hessian**: 自定义二进制协议, 支持引用消除重复
- **Protobuf**: 预编译生成代码, 使用变长编码和字段编号

**推荐选择**:
- **Web API**: JSON(Jackson/Gson)
- **高性能RPC(同语言)**: Kryo
- **跨语言RPC**: Protobuf或Hessian
- **大数据传输**: Protobuf(体积最小)

### 什么是死锁;死锁产生的条件;如何避免死锁

形成死锁的四个必要条件

- 互斥条件: 一个资源每次只能被一个进程使用
- 请求与保持条件: 一个进程因请求资源而阻塞时, 对已获得的资源保持不放
- 不剥夺条件: 进程已获得的资源, 在末使用完之前, 不能强行剥夺
- 循环等待条件: 若干进程之间形成一种头尾相接的循环等待资源关系

处理死锁

预防死锁: 破坏四个必要条件中的一个或多个来预防死锁

- 第一个条件 "互斥" 是不能破坏的, 因为加锁就是为了保证互斥
- 一次性申请所有的资源, 破坏 "占有且等待" 条件
- 占有部分资源的线程进一步申请其他资源时, 如果申请不到, 主动释放它占有的资源, 破坏 "不可 抢占" 条件
- 按序申请资源, 破坏 "循环等待" 条件

避免死锁: 在资源动态分配的过程中, 用某种方式防止系统进入不安全的状态

检测死锁: 运行时产生死锁, 及时发现死锁, 将程序解脱出来

解除死锁: 发生死锁后, 撤销进程, 回收资源, 分配给正在阻塞状态的进程

### 使用反射创建实例和new一个对象的区别

**推荐做法**: 理解反射和new的区别, 根据场景选择合适方式

**区别对比**:

| 特性 | new关键字 | 反射(Reflection) |
|------|----------|-----------------|
| **性能** | 高(编译时确定) | 低(运行时动态查找) |
| **类型检查** | 编译时检查 | 运行时检查 |
| **灵活性** | 低(需知道具体类) | 高(可通过字符串创建) |
| **代码可读性** | 好 | 较差 |
| **安全性** | 高 | 低(可访问私有成员) |

**性能对比**:
```java
// new方式: 直接调用构造函数, 性能高
User user = new User();

// 反射方式: 需要查找类, 检查访问权限, 性能低(慢10-100倍)
Class<?> clazz = Class.forName("com.example.User");
User user = (User) clazz.newInstance(); // 或 clazz.getDeclaredConstructor().newInstance()
```

**使用场景**:
- **new**: 常规对象创建, 性能要求高的场景
- **反射**: 框架开发(Spring IOC), 动态代理, 插件系统

**为什么不推荐反射**:
- 性能开销大, 除非必要否则不使用
- 破坏封装性, 可访问私有成员
- 代码可读性差, 维护困难

### 说一下HashMap和HashTable的区别吧

- HashMap的基类是AbstractMap, 基接口是Map; 而HashTable的基类是Dictionary, 基接口是Map
- HashMap的初始容量是16, 而HashTable的初始容量为11, 负载因子都是0.75; 但是扩容机制不同, HashMap是旧数组长度*2, 而HashTable是旧数组长度 * 2 + 1
- HashMap是线程不安全的, 而HashTable是线程安全的, 因为所有的方法都使用了synchronized
- HashMap使用迭代器迭代, Iterator基于fail-fast机制, 而HashTable可以使用迭代器和枚举
- HashMap的K和V都可以为null, 而HashTable中都不能为null
- HashMap遍历顺序为对数组从左往右遍历, 而HashTable则相反
- 计算理念不同: HashMap 为了性能, 尽量使用位运算; HashTable为了减少冲突, 使得元素位置更加均匀
- HashMap中取消了contains方法和elements方法, 使用containsKey和containsValue, 但是HashTable中四个方法都有
- 对象的定位方法不同
  - HashMap, 使用K的hashCode进行高低16位&运算作为hash值, 和数组的长度减一进行&运算, 得到键值对在数组中的位置, 然后再用equals方法形成链表
  - HashTable使用K的hashCode直接作为hash值和数组长度进行求余运算, 得到键值对在数组中的位置, 然后再用equals方法形成链表
  - 扩容的时候是2的n次方进行扩容, hash值在和2的n次方进行求余运算和&运算的结果一样, 但是&运算要快得多
  - 因为扩容倍数的特殊性, 导致扩容后不需要重新计算键值对在新数组的位置, 只需要判断K的hash值多出来的那一位是0还是1, 如果是0, 新表中键值对的位置和旧表一样, 如果是1 新表中键值对的位置等于旧表的位置 +  旧表的长度
- 关于快速失败 fail-fast: 说的是在juc下的非线程安全集合, (除了Stack、Vector、Enum、HashTable、Properties) 在迭代的过程中一旦别的线程对集合本身进行了结构性的修改(长度变化了), 那么迭代立刻结束, 迭代器将抛出ConcurrentModificationException, 而线程安全的集合和juc包下的集合都是safe-fail

### 进程和线程

### 说一下什么是进程;什么是线程

**推荐做法**: 理解进程和线程的本质区别

**进程(Process)**:
进程是操作系统进行资源分配和调度的基本单位, 是程序的一次执行过程

**特点**:
- 拥有独立的地址空间(内存空间)
- 进程间相互独立, 一个进程崩溃不影响其他进程
- 进程切换开销大(需要切换页表、上下文等)
- 进程间通信(IPC)需要特殊机制

**线程(Thread)**:
线程是CPU调度的基本单位, 是进程内的一个执行流

**特点**:
- 共享进程的地址空间和资源
- 线程间可以直接访问共享数据
- 线程切换开销小(只需切换上下文)
- 一个线程崩溃可能导致整个进程崩溃

**为什么不推荐其他理解**:
- 避免将进程和线程混为一谈, 它们有本质区别
- 理解进程是资源分配单位, 线程是执行单位

### 进程和线程之间的区别是什么

**推荐做法**: 从多个维度理解区别

**核心区别**:

| 维度 | 进程 | 线程 |
|------|------|------|
| **资源拥有** | 独立的内存空间和资源 | 共享进程的资源 |
| **切换开销** | 大(需要切换页表、上下文) | 小(只需切换上下文) |
| **通信方式** | IPC(管道、消息队列等) | 共享内存、信号量等 |
| **独立性** | 相互独立, 崩溃不影响其他进程 | 相互影响, 一个线程崩溃可能影响整个进程 |
| **创建开销** | 大 | 小 |
| **地址空间** | 独立 | 共享 |
| **数量限制** | 受系统资源限制, 数量较少 | 可以创建大量线程 |

**内存模型区别**:
```
进程A: [代码段][数据段][堆][栈]  ← 独立地址空间
进程B: [代码段][数据段][堆][栈]  ← 独立地址空间

进程A:
  线程1: [栈1]  ← 共享代码段、数据段、堆
  线程2: [栈2]  ← 共享代码段、数据段、堆
```

**为什么不推荐其他理解**:
- 避免认为线程是"轻量级进程", 它们有本质区别
- 理解进程提供资源隔离, 线程提供并发执行

### 线程和进程之间的通信方式有哪些

**推荐做法**: 理解不同通信方式的适用场景

**进程间通信(IPC - Inter-Process Communication)**:

1. **管道(Pipe)**
   - 匿名管道: 父子进程间通信
   - 命名管道(FIFO): 无亲缘关系进程间通信
   - 特点: 半双工, 数据流式传输

2. **消息队列(Message Queue)**
   - 进程间发送消息
   - 特点: 异步通信, 支持消息类型

3. **共享内存(Shared Memory)**
   - 多个进程映射同一块内存
   - 特点: 速度最快, 需要同步机制

4. **信号量(Semaphore)**
   - 用于进程间同步
   - 特点: 计数器, 控制资源访问

5. **信号(Signal)**
   - 进程间发送信号
   - 特点: 异步通知机制

6. **套接字(Socket)**
   - 网络通信, 也可用于本地进程间通信
   - 特点: 跨网络, 通用性强

**线程间通信**:

1. **共享内存**
   - 直接访问进程的共享变量
   - 特点: 最简单, 需要同步

2. **消息传递**
   - 通过共享队列传递消息
   - 特点: 解耦, 需要同步

3. **同步机制**
   - synchronized、Lock、volatile
   - 特点: 保证线程安全

**推荐做法**:
- 进程间通信: 优先使用消息队列或共享内存+信号量
- 线程间通信: 使用共享内存+同步机制

### 进程间的通信方式

- 管道
- 消息队列
- 信号量
- 共享内存
- 套接字

### 能举例说一下信号量的使用吗

**推荐做法**: 理解信号量的PV操作, 掌握实际应用场景

**信号量概念**:
信号量是一个计数器, 用于控制多个进程/线程对共享资源的访问

**PV操作**:
- **P操作(Wait)**: 信号量减1, 如果结果小于0, 则阻塞等待
- **V操作(Signal)**: 信号量加1, 如果有等待的进程, 唤醒一个

**Java中的信号量(Semaphore)**:
```java
import java.util.concurrent.Semaphore;

public class SemaphoreExample {
    // 创建信号量, 允许3个线程同时访问
    private static final Semaphore semaphore = new Semaphore(3);
    
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                try {
                    // P操作: 获取许可
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + " 获得资源");
                    
                    // 模拟使用资源
                    Thread.sleep(2000);
                    
                    System.out.println(Thread.currentThread().getName() + " 释放资源");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    // V操作: 释放许可
                    semaphore.release();
                }
            }).start();
        }
    }
}
```

**实际应用场景**:
1. **限流**: 控制同时访问资源的线程数
2. **连接池**: 数据库连接池、HTTP连接池
3. **生产者消费者**: 控制缓冲区大小

**为什么不推荐其他做法**:
- 避免使用synchronized实现限流, Semaphore更灵活
- 避免死锁, 确保acquire和release配对使用

### 共享内存算在进程的内存占用中吗

**推荐做法**: 理解共享内存的内存统计方式

**共享内存的内存统计**:

1. **不算在单个进程内存占用中**
   - 共享内存是独立的内存区域
   - 多个进程映射同一块物理内存
   - 操作系统单独管理共享内存

2. **内存统计方式**:
   ```
   进程A内存 = 进程A私有内存 + 共享内存映射(但不重复计算)
   进程B内存 = 进程B私有内存 + 共享内存映射(但不重复计算)
   系统总内存 = 所有进程私有内存 + 共享内存(只计算一次)
   ```

3. **实际影响**:
   - `top`或`ps`命令显示的内存可能包含共享内存
   - 实际物理内存占用 = 私有内存 + 共享内存(不重复计算)

**Linux中查看**:
```bash
# 查看共享内存
ipcs -m

# 查看进程内存详情(包含共享内存)
cat /proc/PID/smaps
```

**为什么不推荐其他理解**:
- 避免认为共享内存会重复计算, 它只占用一份物理内存
- 理解共享内存是进程间高效通信的方式

### git中pull和fetch的区别

git pull看起来像git fetch+git merge

### socket 和 channel的区别

**推荐做法**: 理解Socket和Channel的本质区别, 根据场景选择

**区别对比**:

| 特性 | Socket | Channel |
|------|--------|---------|
| **API层次** | 操作系统API的封装 | Java NIO抽象 |
| **阻塞模式** | 默认阻塞IO | 支持非阻塞IO |
| **IO模型** | BIO(阻塞IO) | NIO(非阻塞IO) |
| **缓冲区** | 需要手动管理 | 内置Buffer机制 |
| **选择器** | 不支持 | 支持Selector多路复用 |
| **性能** | 较低(阻塞) | 较高(非阻塞+多路复用) |

**Socket示例**:
```java
// 传统Socket - 阻塞IO
ServerSocket serverSocket = new ServerSocket(8080);
Socket socket = serverSocket.accept(); // 阻塞等待连接
InputStream in = socket.getInputStream();
// 读取数据会阻塞
```

**Channel示例**:
```java
// NIO Channel - 非阻塞IO
ServerSocketChannel serverChannel = ServerSocketChannel.open();
serverChannel.configureBlocking(false); // 非阻塞模式
serverChannel.bind(new InetSocketAddress(8080));

Selector selector = Selector.open();
serverChannel.register(selector, SelectionKey.OP_ACCEPT);

// 非阻塞, 可以处理多个连接
while (true) {
    selector.select(); // 阻塞直到有事件
    Set<SelectionKey> keys = selector.selectedKeys();
    // 处理多个连接的事件
}
```

**为什么不推荐其他做法**:
- 高并发场景避免使用传统Socket, 应使用NIO Channel+Selector
- 理解Channel是更高级的抽象, 提供更好的性能

### IO多路复用讲一下

**推荐做法**: 理解IO多路复用的原理和优势

**IO多路复用概念**:
IO多路复用是指一个线程可以同时监听多个文件描述符(如Socket), 当某个描述符就绪时, 通知程序进行读写操作

**传统IO模型的问题**:
```java
// 传统BIO: 一个线程处理一个连接
// 1000个连接需要1000个线程, 线程切换开销大
while (true) {
    Socket socket = serverSocket.accept(); // 阻塞
    new Thread(() -> {
        // 处理连接, 阻塞IO
    }).start();
}
```

**IO多路复用模型**:
```java
// NIO: 一个线程处理多个连接
Selector selector = Selector.open();
serverChannel.register(selector, SelectionKey.OP_ACCEPT);

while (true) {
    selector.select(); // 阻塞直到有事件就绪
    Set<SelectionKey> keys = selector.selectedKeys();
    for (SelectionKey key : keys) {
        if (key.isAcceptable()) {
            // 处理连接
        } else if (key.isReadable()) {
            // 处理读事件
        }
    }
}
```

**IO多路复用优势**:
1. **减少线程数**: 一个线程可以处理多个连接
2. **提高性能**: 减少线程切换开销
3. **资源节约**: 不需要为每个连接创建线程

**Linux实现机制**:
- **select**: 轮询所有文件描述符, 有1024限制
- **poll**: 改进select, 无数量限制, 但仍需轮询
- **epoll**: 事件驱动, 只返回就绪的描述符, 性能最好

**为什么不推荐其他做法**:
- 高并发场景避免使用BIO, 应使用NIO+多路复用
- 理解epoll是Linux下最佳选择, select/poll性能较差

### select描述符限制是多少(1024); 为什么是1024

**推荐做法**: 理解select的限制和原因

**select的限制**:
- **文件描述符数量限制**: 默认1024个
- **原因**: `fd_set`结构体使用位图表示, 大小为1024位(128字节)

**fd_set结构**:
```c
// Linux内核中的定义
typedef struct {
    unsigned long fds_bits[__FD_SETSIZE / __NFDBITS];
} fd_set;

// __FD_SETSIZE 通常定义为 1024
```

**为什么是1024**:
1. **历史原因**: 早期系统资源有限, 1024足够使用
2. **位图实现**: 使用固定大小的位图, 每个bit表示一个文件描述符
3. **性能考虑**: 固定大小便于快速操作

**突破1024限制**:
```c
// 1. 重新定义FD_SETSIZE(不推荐, 需要重新编译)
#define FD_SETSIZE 2048

// 2. 使用poll(推荐)
// poll使用链表, 无数量限制

// 3. 使用epoll(最佳)
// epoll无数量限制, 性能最好
```

**Java中的影响**:
```java
// Java NIO的Selector底层使用epoll(Linux)或kqueue(Mac)
// 不受1024限制, 可以处理大量连接
Selector selector = Selector.open();
// 可以注册数千个Channel
```

**为什么不推荐其他做法**:
- 避免使用select处理大量连接, 应使用epoll
- 理解select是历史遗留, 现代系统应使用epoll/kqueue

### 僵尸进程概念;如何解决

一个进程在调用exit命令结束自己的生命的时候, 其实它并没有真正的被销毁,  而是留下一个称为僵尸进程(Zombie)的数据结构(系统调用exit, 它的作用是 使进程退出, 但也仅仅限于将一个正常的进程变成一个僵尸进程, 并不能将其完全销毁)

- 父进程通过wait和waitpid等函数等待子进程结束, 这会导致父进程挂起
- 如果父进程很忙, 那么可以用signal函数为SIGCHLD安装handler, 因为子进程结束后,  父进程会收到该信号, 可以在handler中调用wait回收
-  如果父进程不关心子进程什么时候结束, 那么可以用signal(SIGCHLD,SIG_IGN) 通知内核, 自己对子进程的结束不感兴趣, 那么子进程结束后, 内核会回收,  并不再给父进程发送信号
-  还有一些技巧, 就是[fork](http://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=7618891)两次, 父进程fork一个子进程, 然后继续工作, 子进程fork一 个孙进程后退出, 那么孙进程被[init](http://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=7727069)接管, 孙进程结束后, init会回收; 不过子进程的回收 还要自己做

### 孤儿进程概念

在操作系统领域中, 孤儿进程指的是在其父进程执行完成或被终止后仍继续运行的一类进程; 这些孤儿进程将被init进程(进程号为1)所收养, 并由init进程对它们完成状态收集工作

### 如果是一个服务器用于存储数据; 然后里面有一些数据是热点数据; 需要缓存; 用哪种引用

热点数据缓存是用软引用

### 匿名内部类如何持有外部类对象

**推荐做法**: 理解内部类持有外部类引用的机制

**内部类持有外部类对象**:
```java
public class Outer {
    private String name = "Outer";
    
    public void method() {
        // 匿名内部类持有外部类引用
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println(name); // 访问外部类成员
            }
        };
    }
}

// 编译后等价于
class Outer$1 implements Runnable {
    private final Outer this$0; // 持有外部类引用
    
    Outer$1(Outer outer) {
        this.this$0 = outer;
    }
    
    @Override
    public void run() {
        System.out.println(this$0.name);
    }
}
```

**内存泄漏风险**:
```java
// 如果内部类生命周期长于外部类, 可能导致内存泄漏
public class Outer {
    private byte[] data = new byte[1024 * 1024]; // 1MB
    
    public Runnable createTask() {
        // 匿名内部类持有Outer引用, 即使Outer不再使用也无法GC
        return new Runnable() {
            @Override
            public void run() {
                System.out.println(data.length);
            }
        };
    }
}

// 使用
Runnable task = outer.createTask();
outer = null; // Outer无法被GC, 因为task持有引用
```

**推荐做法**:
```java
// 1. 使用静态内部类(不持有外部类引用)
public static class StaticInner {
    // 不持有外部类引用
}

// 2. 避免在内部类中持有外部类的大对象引用
// 3. 及时清理内部类引用
```

类和内部类的关系; 内部类为什么可以访问外部类的私有属性

**推荐做法**: 理解内部类访问外部类私有成员的机制

**访问机制**:
- 内部类编译后会生成`access$XXX`方法访问外部类私有成员
- JVM允许同一类文件中的类访问彼此的私有成员

```java
public class Outer {
    private String name = "Outer";
    
    class Inner {
        void print() {
            System.out.println(name); // 访问私有成员
        }
    }
}

// 编译后生成
class Outer {
    private String name;
    
    // 编译器生成的访问方法
    static String access$000(Outer outer) {
        return outer.name;
    }
}

class Outer$Inner {
    void print() {
        System.out.println(Outer.access$000(this.this$0));
    }
}
```

### 内存泄漏的场景

**推荐做法**: 识别常见内存泄漏场景, 避免问题

**常见内存泄漏场景**:

1. **集合类持有对象引用**
   ```java
   // 错误: 对象不再使用但仍在集合中
   List<Object> list = new ArrayList<>();
   Object obj = new Object();
   list.add(obj);
   obj = null; // 对象无法GC, 因为list仍持有引用
   
   // 解决: 及时从集合中移除
   list.remove(obj);
   ```

2. **监听器未移除**
   ```java
   // 错误: 监听器持有外部类引用
   button.addActionListener(new ActionListener() {
       @Override
       public void actionPerformed(ActionEvent e) {
           // 持有外部类引用
       }
   });
   // 忘记移除监听器
   
   // 解决: 使用弱引用或及时移除
   ```

3. **ThreadLocal未清理**
   ```java
   // 错误: ThreadLocal使用后未remove
   ThreadLocal<String> threadLocal = new ThreadLocal<>();
   threadLocal.set("value");
   // 忘记remove, 导致内存泄漏
   
   // 解决: 使用try-finally确保清理
   try {
       threadLocal.set("value");
   } finally {
       threadLocal.remove();
   }
   ```

4. **内部类持有外部类引用**
   ```java
   // 错误: 内部类生命周期长于外部类
   public class Outer {
       private byte[] data = new byte[1024 * 1024];
       
       public Runnable createTask() {
           return new Runnable() {
               @Override
               public void run() {
                   System.out.println(data.length); // 持有引用
               }
           };
       }
   }
   ```

5. **缓存未设置过期或大小限制**
   ```java
   // 错误: 缓存无限增长
   Map<String, Object> cache = new HashMap<>();
   // 不断添加, 永不清理
   
   // 解决: 使用LRU缓存或设置过期
   Map<String, Object> cache = new LinkedHashMap<String, Object>(16, 0.75f, true) {
       @Override
       protected boolean removeEldestEntry(Map.Entry eldest) {
           return size() > 1000; // 限制大小
       }
   };
   ```

**为什么不推荐其他做法**:
- 避免使用强引用缓存, 应使用WeakReference或SoftReference
- 避免忘记清理资源, 使用try-finally或try-with-resources

### 内存泄漏的概念

**推荐做法**: 理解内存泄漏的本质

**内存泄漏定义**:
内存泄漏是指程序中已动态分配的堆内存由于某种原因程序未释放或无法释放, 造成系统内存的浪费, 导致程序运行速度减慢甚至系统崩溃等严重后果

**关键特征**:
1. **对象不再使用**: 程序逻辑上不再需要该对象
2. **无法被GC**: 对象仍被引用, GC无法回收
3. **持续增长**: 内存占用持续增加, 最终导致OOM

**与内存溢出的区别**:
- **内存泄漏**: 对象无法被回收, 内存逐渐耗尽
- **内存溢出**: 内存不足, 无法分配新对象

**检测方法**:
```bash
# 1. 使用jmap生成堆转储
jmap -dump:format=b,file=heap.hprof <pid>

# 2. 使用MAT分析
# 查找Dominator Tree, 找出占用内存最大的对象

# 3. 使用VisualVM监控
# 观察堆内存趋势, 如果持续增长可能存在泄漏
```

**为什么不推荐其他理解**:
- 避免将内存溢出等同于内存泄漏
- 理解内存泄漏是渐进的过程, 需要长期监控

### main方法执行之前发生了什么

**推荐做法**: 理解JVM启动过程和类加载机制

**main方法执行前的步骤**:

1. **JVM启动**
   - 加载JVM动态库
   - 初始化JVM运行时环境

2. **类加载器初始化**
   - 创建Bootstrap ClassLoader
   - 创建Extension ClassLoader
   - 创建Application ClassLoader

3. **加载主类**
   ```java
   // JVM会加载包含main方法的类
   // 触发类的加载、链接、初始化
   ```

4. **类加载过程**
   - **加载**: 查找并加载类的二进制数据
   - **验证**: 验证class文件格式
   - **准备**: 为类变量分配内存并设置默认值
   - **解析**: 将符号引用转换为直接引用
   - **初始化**: 执行类构造器`<clinit>()`方法

5. **执行main方法**
   ```java
   public static void main(String[] args) {
       // 此时类已完全加载和初始化
   }
   ```

**详细流程**:
```
1. JVM启动
   ↓
2. 创建类加载器层次结构
   ↓
3. 查找main方法所在类
   ↓
4. 加载主类(触发类加载过程)
   ↓
5. 链接(验证、准备、解析)
   ↓
6. 初始化(执行静态代码块、静态变量赋值)
   ↓
7. 执行main方法
```

**验证示例**:
```java
public class Test {
    static {
        System.out.println("静态代码块执行"); // main之前执行
    }
    
    private static String name = initName(); // main之前执行
    
    private static String initName() {
        System.out.println("静态变量初始化");
        return "Test";
    }
    
    public static void main(String[] args) {
        System.out.println("main方法执行");
    }
}
// 输出顺序:
// 静态代码块执行
// 静态变量初始化
// main方法执行
```

**为什么不推荐其他理解**:
- 避免认为main是程序入口就忽略类加载过程
- 理解类加载是JVM的核心机制

### 线程的从用户态和核心态的具体过程是什么

**推荐做法**: 理解用户态和内核态切换的机制

**用户态和内核态**:

1. **用户态(User Mode)**
   - 应用程序运行在用户态
   - 只能访问受限资源
   - 不能直接访问硬件

2. **内核态(Kernel Mode)**
   - 操作系统运行在内核态
   - 可以访问所有资源
   - 可以执行特权指令

**线程切换过程**:

1. **用户态 → 内核态(系统调用)**
   ```
   用户线程调用系统调用(如read、write)
   ↓
   触发中断/异常(INT 0x80或SYSCALL指令)
   ↓
   CPU切换到内核态
   ↓
   执行系统调用处理程序
   ↓
   访问内核资源
   ```

2. **内核态 → 用户态(返回)**
   ```
   系统调用执行完成
   ↓
   设置返回值
   ↓
   执行IRET指令返回用户态
   ↓
   恢复用户态上下文
   ```

**线程切换详细过程**:
```
1. 保存当前线程上下文(用户态)
   - 寄存器状态
   - 程序计数器
   - 栈指针
   ↓
2. 切换到内核态
   - 保存用户态寄存器
   - 加载内核栈
   ↓
3. 调度器选择下一个线程
   - 从就绪队列选择
   - 检查线程优先级
   ↓
4. 恢复下一个线程上下文
   - 加载寄存器
   - 恢复栈指针
   ↓
5. 切换到用户态
   - 加载用户态寄存器
   - 跳转到新线程的指令
```

**Java中的体现**:
```java
// 文件IO操作会触发用户态到内核态切换
FileInputStream fis = new FileInputStream("file.txt");
fis.read(); // 系统调用, 切换到内核态

// 网络IO也会切换
Socket socket = new Socket("host", 8080);
socket.getInputStream().read(); // 系统调用
```

**性能影响**:
- 用户态切换开销: 较小(纳秒级)
- 内核态切换开销: 较大(微秒级), 需要保存/恢复更多上下文

**为什么不推荐其他理解**:
- 避免忽略系统调用的开销
- 理解IO操作会触发内核态切换, 影响性能

### finalize 的作用

**推荐做法**: 理解finalize的机制, 但不推荐使用

**finalize方法作用**:
`finalize()`是Object类的一个protected方法, 在对象被GC回收之前会被调用

**执行时机**:
```java
public class Resource {
    @Override
    protected void finalize() throws Throwable {
        // 对象被GC回收前调用
        // 可以在这里释放资源
        super.finalize();
    }
}
```

**为什么不推荐使用finalize**:

1. **执行时机不确定**
   - GC时机不确定, finalize执行时机也不确定
   - 可能永远不会执行

2. **性能开销大**
   - finalize会延迟对象回收
   - 对象需要两次GC才能回收

3. **可能抛出异常**
   - finalize中异常会被忽略
   - 可能导致资源泄漏

4. **无法保证执行顺序**
   - 多个对象的finalize执行顺序不确定

**推荐做法**:
```java
// 1. 使用try-finally确保资源释放
try {
    // 使用资源
} finally {
    // 释放资源
}

// 2. 使用try-with-resources(Java 7+)
try (Resource resource = new Resource()) {
    // 使用资源, 自动关闭
}

// 3. 实现AutoCloseable接口
public class Resource implements AutoCloseable {
    @Override
    public void close() throws Exception {
        // 释放资源
    }
}
```

**finalize的替代方案**:
- **Cleaner(Java 9+)**: 更可靠的清理机制
- **PhantomReference**: 对象回收前的通知机制

**为什么不推荐使用finalize**:
- finalize已被标记为deprecated(Java 9+)
- 使用try-with-resources或Cleaner更可靠

### BIO, NIO, 和AIO都了解哪些, 各种IO讲一下

**推荐做法**: 理解三种IO模型, 根据场景选择

**三种IO模型对比**:

| 特性 | BIO(Blocking IO) | NIO(Non-blocking IO) | AIO(Asynchronous IO) |
|------|-----------------|---------------------|---------------------|
| **阻塞方式** | 阻塞 | 非阻塞 | 异步非阻塞 |
| **线程模型** | 一个线程一个连接 | 一个线程多个连接 | 回调机制 |
| **性能** | 低 | 中 | 高 |
| **复杂度** | 低 | 中 | 高 |
| **适用场景** | 连接数少 | 连接数多 | 连接数非常多 |

**BIO(阻塞IO)**:
```java
// 传统BIO: 阻塞等待
ServerSocket serverSocket = new ServerSocket(8080);
while (true) {
    Socket socket = serverSocket.accept(); // 阻塞等待连接
    new Thread(() -> {
        try {
            InputStream in = socket.getInputStream();
            byte[] buffer = new byte[1024];
            in.read(buffer); // 阻塞等待数据
        } catch (IOException e) {
            e.printStackTrace();
        }
    }).start();
}
// 缺点: 每个连接需要一个线程, 线程开销大
```

**NIO(非阻塞IO)**:
```java
// NIO: 非阻塞+多路复用
ServerSocketChannel serverChannel = ServerSocketChannel.open();
serverChannel.configureBlocking(false); // 非阻塞
serverChannel.bind(new InetSocketAddress(8080));

Selector selector = Selector.open();
serverChannel.register(selector, SelectionKey.OP_ACCEPT);

while (true) {
    selector.select(); // 阻塞直到有事件
    Set<SelectionKey> keys = selector.selectedKeys();
    for (SelectionKey key : keys) {
        if (key.isAcceptable()) {
            // 处理连接
        } else if (key.isReadable()) {
            // 处理读事件, 非阻塞
        }
    }
}
// 优点: 一个线程处理多个连接
```

**AIO(异步IO)**:
```java
// AIO: 异步回调
AsynchronousServerSocketChannel serverChannel = 
    AsynchronousServerSocketChannel.open();
serverChannel.bind(new InetSocketAddress(8080));

serverChannel.accept(null, new CompletionHandler<AsynchronousSocketChannel, Void>() {
    @Override
    public void completed(AsynchronousSocketChannel channel, Void attachment) {
        // 连接建立回调
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        channel.read(buffer, buffer, new CompletionHandler<Integer, ByteBuffer>() {
            @Override
            public void completed(Integer result, ByteBuffer buffer) {
                // 数据读取完成回调
            }
            
            @Override
            public void failed(Throwable exc, ByteBuffer buffer) {
                // 读取失败回调
            }
        });
    }
    
    @Override
    public void failed(Throwable exc, Void attachment) {
        // 连接失败回调
    }
});
// 优点: 完全异步, 性能最高
```

**选择建议**:
- **BIO**: 连接数少(<100), 简单场景
- **NIO**: 连接数中等(100-10000), 高并发场景(推荐)
- **AIO**: 连接数非常多(>10000), 但Linux支持不完善

**为什么不推荐其他做法**:
- 高并发场景避免使用BIO, 线程开销太大
- Linux下AIO支持不完善, 推荐使用NIO

### ReentrantLock 和 ReentrantReadWriteLock 有什么区别

java.util.concurrent.locks.ReentrantReadWriteLock implements ReadWriteLock, java.io.Serializable

java.util.concurrent.locks.ReentrantLock implements Lock, java.io.Serializable

相同

- 可重入性
- 公平性选择
- 都基于 AQS实现
- 都需要手动释放锁

不同

| 特性       | ReentrantLock  | ReentrantReadWriteLock                   |
| ---------- | -------------- | ---------------------------------------- |
| 锁类型     | 独占锁         | 读写锁(包含读锁和写锁)                 |
| 并发性能   | 完全互斥       | 读读不互斥, 读写、写写互斥               |
| 适用场景   | 完全互斥的场景 | 读多写少的场景                           |
| 锁降级     | 不支持         | 支持（从写锁降级为读锁)                 |
| 锁获取方式 | lock()         | readLock() 和 writeLock() 分别获取不同锁 |

- 当只有写操作或读写操作频率相当时, 使用 `ReentrantLock`
- 当读操作远多于写操作时, 使用 `ReentrantReadWriteLock` 可以提高并发性能

在极高并发场景下, `ReentrantReadWriteLock` 的读锁可能导致写锁饥饿, Java 8 引入了 `StampedLock` 作为替代方案之一

### 说下StampedLock

https://blog.csdn.net/qq_37883866/article/details/140664358

## MQ

### rabbitmq的使用场景有哪些

**推荐做法**: 理解RabbitMQ的核心价值, 根据业务场景选择使用

**主要使用场景**:

1. **异步处理**
   - 耗时操作异步化, 提升用户体验
   - 例如: 用户注册后发送邮件、短信通知

2. **应用解耦**
   - 系统间通过消息队列通信, 降低耦合度
   - 例如: 订单系统与库存系统、支付系统解耦

3. **流量削峰**
   - 突发流量缓冲, 保护下游系统
   - 例如: 秒杀活动, 将请求先放入队列

4. **日志收集**
   - 集中式日志处理
   - 例如: 多个服务将日志发送到消息队列, 统一处理

5. **消息广播**
   - 一对多消息分发
   - 例如: 配置变更通知多个服务

**为什么不推荐其他做法**:
- 避免同步调用导致系统耦合, 应使用消息队列解耦
- 避免直接处理突发流量, 应使用队列削峰

### rabbitmq 有哪些重要的角色

**推荐做法**: 理解RabbitMQ的核心角色和职责

**重要角色**:

1. **Producer(生产者)**
   - 消息的发送方
   - 将消息发送到Exchange

2. **Consumer(消费者)**
   - 消息的接收方
   - 从Queue中获取消息并处理

3. **Broker(消息代理)**
   - RabbitMQ服务器本身
   - 接收、存储、转发消息

4. **Exchange(交换机)**
   - 接收生产者消息, 根据路由规则转发到Queue
   - 类型: direct、topic、fanout、headers

5. **Queue(队列)**
   - 存储消息的缓冲区
   - 消费者从Queue中获取消息

6. **Binding(绑定)**
   - Exchange和Queue之间的关联关系
   - 定义路由规则

**为什么不推荐其他理解**:
- 避免混淆Exchange和Queue的作用, Exchange负责路由, Queue负责存储

### rabbitmq 有哪些重要的组件

**推荐做法**: 理解RabbitMQ的组件架构

**核心组件**:

1. **Connection(连接)**
   - 客户端与Broker之间的TCP连接
   - 一个应用通常维护一个长连接

2. **Channel(通道)**
   - 连接中的虚拟通道
   - 多个Channel共享一个Connection, 减少TCP连接数

3. **Virtual Host(虚拟主机)**
   - 逻辑隔离, 类似命名空间
   - 不同vhost的Exchange、Queue相互隔离

4. **Exchange Types(交换机类型)**
   - **direct**: 精确匹配routing key
   - **topic**: 模式匹配routing key
   - **fanout**: 广播到所有绑定的Queue
   - **headers**: 基于消息头匹配

5. **Message(消息)**
   - 包含payload和properties
   - properties包括routing key、priority、expiration等

**为什么不推荐其他理解**:
- 避免在同一个Connection中创建过多Channel, 应合理复用

### rabbitmq 中vhost 的作用是什么

**推荐做法**: 理解vhost的隔离作用, 合理规划多租户场景

**vhost的作用**:

1. **逻辑隔离**
   - 不同vhost的Exchange、Queue、Binding完全隔离
   - 类似数据库的schema概念

2. **权限控制**
   - 可以为不同用户分配不同vhost的访问权限
   - 实现多租户场景下的资源隔离

3. **资源管理**
   - 不同业务使用不同vhost, 便于管理和监控
   - 可以独立设置vhost的资源限制

**使用示例**:
```java
// 连接指定vhost
ConnectionFactory factory = new ConnectionFactory();
factory.setHost("localhost");
factory.setVirtualHost("/production"); // 指定vhost
Connection connection = factory.newConnection();
```

**为什么不推荐其他做法**:
- 避免所有业务使用默认vhost, 应合理划分vhost实现隔离
- 避免vhost过多导致管理复杂, 应根据业务模块划分

### rabbitmq 的消息是怎么发送的

**推荐做法**: 理解消息发送流程, 正确使用RabbitMQ API

**消息发送流程**:

1. **Producer发送消息到Exchange**
   ```java
   channel.basicPublish(exchange, routingKey, props, messageBody);
   ```

2. **Exchange根据类型和routing key路由消息**
   - direct: 精确匹配routing key
   - topic: 模式匹配routing key
   - fanout: 忽略routing key, 广播到所有Queue
   - headers: 匹配消息头

3. **消息到达绑定的Queue**
   - Exchange根据Binding规则将消息路由到Queue

4. **Consumer从Queue获取消息**
   ```java
   channel.basicConsume(queueName, autoAck, consumer);
   ```

**完整示例**:
```java
// 生产者
ConnectionFactory factory = new ConnectionFactory();
factory.setHost("localhost");
try (Connection connection = factory.newConnection();
     Channel channel = connection.createChannel()) {
    
    // 声明Exchange
    channel.exchangeDeclare("myExchange", "direct");
    
    // 发送消息
    String message = "Hello RabbitMQ";
    channel.basicPublish("myExchange", "routing.key", null, 
                        message.getBytes());
}

// 消费者
channel.queueDeclare("myQueue", false, false, false, null);
channel.queueBind("myQueue", "myExchange", "routing.key");

DeliverCallback deliverCallback = (consumerTag, delivery) -> {
    String message = new String(delivery.getBody(), "UTF-8");
    System.out.println("Received: " + message);
    channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
};
channel.basicConsume("myQueue", false, deliverCallback, 
                    consumerTag -> {});
```

**为什么不推荐其他做法**:
- 避免直接发送到Queue(除非使用默认Exchange), 应通过Exchange路由
- 避免忘记声明Exchange和Queue, 应确保先声明再使用

### rabbitmq 怎么保证消息的稳定性

**推荐做法**: 使用持久化、确认机制、事务等保证消息可靠性

**保证消息稳定性的措施**:

1. **消息持久化**
   ```java
   // Exchange持久化
   channel.exchangeDeclare("myExchange", "direct", true);
   
   // Queue持久化
   channel.queueDeclare("myQueue", true, false, false, null);
   
   // 消息持久化
   AMQP.BasicProperties props = new AMQP.BasicProperties.Builder()
       .deliveryMode(2) // 2表示持久化
       .build();
   channel.basicPublish("myExchange", "routing.key", props, message);
   ```

2. **Publisher Confirm机制**
   ```java
   // 开启Confirm模式
   channel.confirmSelect();
   
   // 异步确认
   channel.addConfirmListener((deliveryTag, multiple) -> {
       // 消息发送成功
   }, (deliveryTag, multiple) -> {
       // 消息发送失败, 需要重试
   });
   ```

3. **Consumer Ack机制**
   ```java
   // 手动确认, 处理完消息后ack
   channel.basicConsume("myQueue", false, (tag, delivery) -> {
       try {
           // 处理消息
           processMessage(delivery.getBody());
           // 确认消息
           channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
       } catch (Exception e) {
           // 拒绝消息, 重新入队
           channel.basicNack(delivery.getEnvelope().getDeliveryTag(), 
                           false, true);
       }
   }, tag -> {});
   ```

4. **事务机制(性能较差, 不推荐)**
   ```java
   channel.txSelect();
   try {
       channel.basicPublish(...);
       channel.txCommit();
   } catch (Exception e) {
       channel.txRollback();
   }
   ```

**为什么不推荐其他做法**:
- 避免使用自动ack(autoAck=true), 消息处理失败会丢失
- 避免使用事务保证可靠性, 性能差, 应使用Publisher Confirm

### rabbitmq 怎么避免消息丢失

**推荐做法**: 从生产者、Broker、消费者三个层面保证消息不丢失

**避免消息丢失的措施**:

1. **生产者层面**
   - 使用Publisher Confirm机制
   - 消息持久化(deliveryMode=2)
   - 实现重试机制

2. **Broker层面**
   - Exchange、Queue持久化
   - 消息持久化到磁盘
   - 镜像队列(高可用)

3. **消费者层面**
   - 使用手动ack
   - 处理完消息再ack
   - 异常时nack并重新入队

**完整示例**:
```java
// 生产者: 保证消息发送成功
channel.confirmSelect();
channel.addConfirmListener((tag, multiple) -> {
    // 成功
}, (tag, multiple) -> {
    // 失败, 重试
    retryPublish(tag);
});

AMQP.BasicProperties props = new AMQP.BasicProperties.Builder()
    .deliveryMode(2) // 持久化
    .build();
channel.basicPublish("exchange", "routingKey", props, message);

// 消费者: 保证消息处理成功
channel.basicConsume("queue", false, (tag, delivery) -> {
    try {
        processMessage(delivery.getBody());
        channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
    } catch (Exception e) {
        // 处理失败, 重新入队
        channel.basicNack(delivery.getEnvelope().getDeliveryTag(), 
                         false, true);
    }
}, tag -> {});
```

**为什么不推荐其他做法**:
- 避免使用自动ack, 消息处理失败会丢失
- 避免消息不持久化, Broker重启会丢失消息

### 消息持久化成功的条件有哪些

**推荐做法**: 理解持久化的完整条件, 确保消息真正持久化

**消息持久化成功的条件**:

1. **Exchange持久化**
   ```java
   channel.exchangeDeclare("exchange", "direct", true); // durable=true
   ```

2. **Queue持久化**
   ```java
   channel.queueDeclare("queue", true, false, false, null); // durable=true
   ```

3. **消息持久化**
   ```java
   AMQP.BasicProperties props = new AMQP.BasicProperties.Builder()
       .deliveryMode(2) // 2=持久化, 1=非持久化
       .build();
   ```

4. **三者缺一不可**
   - Exchange、Queue、Message都必须持久化
   - 缺少任何一个, 消息都可能丢失

**验证持久化**:
```bash
# 重启RabbitMQ后, 消息仍然存在
rabbitmqctl stop_app
rabbitmqctl start_app
```

**为什么不推荐其他做法**:
- 避免只持久化消息而Exchange/Queue不持久化, Broker重启后Exchange/Queue消失
- 避免持久化配置不一致, 确保三者都持久化

### rabbitmq  持久化有什么缺点

**推荐做法**: 理解持久化的性能影响, 权衡可靠性和性能

**持久化的缺点**:

1. **性能下降**
   - 消息需要写入磁盘, 比内存操作慢
   - 吞吐量下降约10倍

2. **磁盘IO压力**
   - 频繁的磁盘写入操作
   - 可能成为性能瓶颈

3. **资源消耗**
   - 磁盘空间占用
   - 内存中也需要缓存消息

**性能对比**:
- 非持久化: 吞吐量可达数万QPS
- 持久化: 吞吐量降至数千QPS

**优化建议**:
- 使用SSD提升IO性能
- 合理设置队列长度, 避免消息堆积
- 非关键消息可以不持久化

**为什么不推荐其他做法**:
- 避免所有消息都持久化, 应根据业务重要性选择
- 避免过度追求性能而放弃持久化, 关键消息必须持久化

### rabbitmq  有几种广播类型

**推荐做法**: 理解不同Exchange类型, 根据场景选择

**广播类型(Exchange类型)**:

1. **fanout(扇出)**
   - 广播到所有绑定的Queue
   - 忽略routing key
   - 适用场景: 消息广播

2. **direct(直连)**
   - 精确匹配routing key
   - 适用场景: 点对点消息

3. **topic(主题)**
   - 模式匹配routing key
   - 支持通配符: *匹配一个词, #匹配多个词
   - 适用场景: 消息分类订阅

4. **headers(头匹配)**
   - 基于消息头匹配
   - 不常用

**示例**:
```java
// fanout: 广播
channel.exchangeDeclare("logs", "fanout");
channel.basicPublish("logs", "", null, message); // routing key为空

// direct: 精确匹配
channel.exchangeDeclare("direct_logs", "direct");
channel.basicPublish("direct_logs", "error", null, message);

// topic: 模式匹配
channel.exchangeDeclare("topic_logs", "topic");
channel.basicPublish("topic_logs", "user.login", null, message);
// 可以匹配: user.*, user.#, *.login等
```

**为什么不推荐其他做法**:
- 避免所有场景都用direct, 应根据消息分发模式选择Exchange类型

### rabbitmq  节点的类型有哪些

**推荐做法**: 理解节点类型, 合理规划集群架构

**节点类型**:

1. **磁盘节点(Disk Node)**
   - 元数据存储在磁盘
   - 集群中至少需要一个磁盘节点
   - 负责集群元数据的持久化

2. **内存节点(RAM Node)**
   - 元数据存储在内存
   - 性能更好, 但重启后元数据丢失
   - 集群中可以有多个内存节点

**节点类型选择**:
- 磁盘节点: 适合存储集群配置、队列定义等元数据
- 内存节点: 适合高吞吐量场景, 但需要至少一个磁盘节点

**为什么不推荐其他做法**:
- 避免集群中只有内存节点, 必须至少有一个磁盘节点
- 避免所有节点都是磁盘节点, 合理使用内存节点提升性能

### rabbitmq  集群搭建需要注意哪些问题

**推荐做法**: 理解集群搭建的关键点, 避免常见问题

**集群搭建注意事项**:

1. **Erlang Cookie一致**
   - 所有节点的Erlang Cookie必须相同
   - 文件位置: `$HOME/.erlang.cookie`

2. **至少一个磁盘节点**
   - 集群中必须至少有一个磁盘节点
   - 磁盘节点负责元数据持久化

3. **节点名称规范**
   - 使用`rabbit@hostname`格式
   - 确保hostname可解析

4. **网络连通性**
   - 节点间需要开放端口: 4369(epmd), 25672(集群通信)
   - 确保防火墙规则正确

5. **镜像队列配置**
   ```bash
   # 设置镜像队列
   rabbitmqctl set_policy ha-all "^" '{"ha-mode":"all"}'
   ```

6. **集群启动顺序**
   - 先启动磁盘节点
   - 再启动其他节点并加入集群

**为什么不推荐其他做法**:
- 避免Erlang Cookie不一致导致节点无法加入集群
- 避免没有磁盘节点导致元数据丢失

### rabbitmq  每个节点是其他节点的完整拷贝吗

**推荐做法**: 理解RabbitMQ集群的数据分布机制

**集群数据分布**:

1. **元数据同步**
   - Exchange、Queue定义等元数据在所有节点同步
   - 但消息内容不复制

2. **消息存储**
   - 消息只存储在创建该Queue的节点
   - 其他节点不存储消息内容

3. **镜像队列**
   - 配置镜像队列后, 消息会复制到多个节点
   - 提供高可用性

**数据分布示例**:
```
节点A: Queue1的消息
节点B: Queue2的消息
节点C: Queue3的消息
(如果没有镜像队列)
```

**为什么不推荐其他理解**:
- 避免认为所有节点都有完整数据拷贝, 默认情况下消息不复制
- 需要高可用时应配置镜像队列

### rabbitmq  集群中唯一一个磁盘节点崩溃了会怎样

**推荐做法**: 理解磁盘节点的重要性, 避免单点故障

**唯一磁盘节点崩溃的影响**:

1. **集群无法变更**
   - 无法创建新的Exchange、Queue
   - 无法修改集群配置

2. **消息可能丢失**
   - 如果消息未持久化, 会丢失
   - 如果消息已持久化, 磁盘节点恢复后可恢复

3. **集群仍可运行**
   - 现有Queue仍可收发消息
   - 但无法进行集群管理操作

**解决方案**:
- 集群中至少配置2个磁盘节点
- 使用镜像队列保证高可用
- 定期备份元数据

**为什么不推荐其他做法**:
- 避免集群中只有一个磁盘节点, 应配置多个磁盘节点避免单点故障

### rabbitmq  对集群节点停止顺序有要求吗

**推荐做法**: 理解集群节点停止的正确顺序, 避免数据丢失

**节点停止顺序**:

1. **推荐顺序**
   - 先停止内存节点
   - 最后停止磁盘节点

2. **原因**
   - 磁盘节点负责元数据持久化
   - 最后停止磁盘节点可以确保元数据已保存

3. **强制停止的影响**
   - 如果强制停止磁盘节点, 可能导致元数据不一致
   - 需要从其他节点恢复或重新搭建

**停止命令**:
```bash
# 优雅停止
rabbitmqctl stop

# 强制停止(不推荐)
rabbitmqctl stop_app
```

**为什么不推荐其他做法**:
- 避免先停止磁盘节点, 可能导致元数据丢失
- 避免强制停止节点, 应优雅停止

### 消息队列如何保证消息可靠传输

**推荐做法**: 从生产者、Broker、消费者三个层面保证消息可靠传输

**保证消息可靠传输的措施**:

1. **生产者可靠性**
   - Publisher Confirm机制
   - 消息持久化
   - 重试机制

2. **Broker可靠性**
   - Exchange、Queue持久化
   - 消息持久化
   - 镜像队列(高可用)
   - 集群部署

3. **消费者可靠性**
   - 手动ack
   - 处理完消息再ack
   - 异常时nack并重新入队
   - 幂等性处理

**完整可靠性方案**:
```java
// 1. 生产者: Publisher Confirm + 持久化
channel.confirmSelect();
AMQP.BasicProperties props = new AMQP.BasicProperties.Builder()
    .deliveryMode(2)
    .build();
channel.basicPublish("exchange", "routingKey", props, message);

// 2. Broker: 持久化配置
channel.exchangeDeclare("exchange", "direct", true);
channel.queueDeclare("queue", true, false, false, null);

// 3. 消费者: 手动ack + 幂等处理
channel.basicConsume("queue", false, (tag, delivery) -> {
    try {
        if (processMessageIdempotent(delivery.getBody())) {
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
        } else {
            channel.basicNack(delivery.getEnvelope().getDeliveryTag(), 
                             false, true);
        }
    } catch (Exception e) {
        channel.basicNack(delivery.getEnvelope().getDeliveryTag(), 
                         false, true);
    }
}, tag -> {});
```

**为什么不推荐其他做法**:
- 避免只保证一个层面的可靠性, 应三个层面都保证
- 避免忽略幂等性, 消息可能重复消费

## Redis

### redis是什么; 都有哪些使用场景

**推荐做法**: 理解Redis的核心特性和适用场景

**Redis是什么**:
Redis(Remote Dictionary Server)是一个开源的内存数据结构存储系统, 可以用作数据库、缓存和消息中间件

**核心特性**:
- 基于内存存储, 读写速度快
- 支持多种数据结构: String、Hash、List、Set、Sorted Set等
- 支持持久化(RDB、AOF)
- 支持主从复制、哨兵、集群等高可用方案

**主要使用场景**:

1. **缓存**
   - 热点数据缓存, 减轻数据库压力
   - 提升系统响应速度

2. **会话存储**
   - 存储用户Session信息
   - 分布式系统共享Session

3. **计数器**
   - 实时统计、点赞数、访问量等
   - 使用INCR命令实现原子性操作

4. **排行榜**
   - 使用Sorted Set实现排行榜
   - 支持按分数排序

5. **消息队列**
   - 使用List实现简单的消息队列
   - Pub/Sub实现发布订阅

6. **分布式锁**
   - 使用SET NX实现分布式锁
   - 保证分布式系统数据一致性

7. **限流**
   - 使用滑动窗口、令牌桶等算法
   - 防止系统过载

**为什么不推荐其他做法**:
- 避免将所有数据都放入Redis, 应根据访问频率选择
- 避免使用Redis存储大量冷数据, 应合理设置过期时间

### redis 单线程问题

Redis 是单线程的, 主要是指 Redis 的网络 IO 和键值对读写是由一个线程来完成的; 但 Redis的其他功能, 比如持久化, 异步删除, 集群数据同步等其实是由额外的线程执行的, 所以 Redis 严格来说并不是单线程的

Redis 为什么用单线程: 减少多线程带来的额外开销和多线程同时访问共享资源的并发问题

单线程 Redis 为什么那么快:

- Redis 大部分操作在内存上完成
- 采用了高效的数据结构
- 采用了 多路复用机制

### 基于多路复用的高性能 I/O模型

Linux 中的 IO 多路复用机制是指一个线程处理多个 IO 流, 就是我们经常听到的 select/epoll 机制

该机制允许内核中, 同时存在多个监听套接字和已连接套接字, 内核会一直监听这些套接字上的连接请求或数据请求, 一旦有请求到达, 就会交给 Redis 线程处理, 这就实现了一个 Redis 线程处理多个 IO 流的效果

为了在请求到达时能通知到 Redis 线程, select/epoll 提供了基于事件的回调机制, 即针对不同事件的发生, 调用相应的处理函数

### Redis 基本 IO 模型, 还有哪些潜在的性能瓶颈

Redis单线程处理IO请求性能瓶颈主要包括2个方面: 

任意一个请求在server中一旦发生耗时, 都会影响整个server的性能, 也就是说后面的请求都要等前面这个耗时请求处理完成, 自己才能被处理到, 耗时的操作包括以下几种

- 操作bigkey: 写入一个bigkey在分配内存时需要消耗更多的时间, 同样, 删除bigkey释放内存同样会产生耗时;
- 使用复杂度过高的命令: 例如SORT/SUNION/ZUNIONSTORE, 或者O(N)命令, 但是N很大, 例如lrange key 0 -1一次查询全量数据
- 大量key集中过期: Redis的过期机制也是在主线程中执行的, 大量key集中过期会导致处理一个请求时, 耗时都在删除过期key, 耗时变长;
- 淘汰策略: 淘汰策略也是在主线程执行的, 当内存超过Redis内存上限后, 每次写入都需要淘汰一些key, 也会造成耗时变长
- AOF刷盘开启always机制: 每次写入都需要把这个操作刷到磁盘, 写磁盘的速度远比写内存慢, 会拖慢Redis的性能；
- 主从全量同步生成RDB: 虽然采用fork子进程生成数据快照, 但fork这一瞬间也是会阻塞整个线程的, 实例越大, 阻塞时间越;

并发量非常大时, 单线程读写客户端IO数据存在性能瓶颈, 虽然采用IO多路复用机制, 但是读写客户端数据依旧是同步IO, 只能单线程依次读取客户端的数据, 无法利用到CPU多核; 

针对问题1, 一方面需要业务人员去规避, 一方面Redis在4.0推出了lazy-free机制, 把bigkey释放内存的耗时操作放在了异步线程中执行, 降低对主线程的影响

针对问题2, Redis在6.0推出了多线程, 可以在高并发场景下利用CPU多核多线程读写客户端数据, 进一步提升server性能, 当然, 只是针对客户端的读写是并行的, 每个命令的真正操作依旧是单线程的

### 缓存雪崩问题

缓存雪崩是指在同一时间段内大量的缓存key同时失效或者redis服务宕机 导致大量的请求到达数据库

原因: 

- 缓存系统本身不可用 导致大量请求直接回源到数据库
- 应用设计层面大量的 key 在同一时期过期 导致大量的数据回源

解决

- 给不同的 key 的 TTL 添加随机值
- 利用 redis 集群提高服务的可用性
- 给缓存业务添加降级限流策略
- 给业务添加多级缓存

### 什么是缓存穿透; 怎么解决

缓存穿透是指查询⼀个⼀定不存在的数据, 由于缓存不命中时需要从数据库查询, 查不到数据则不写⼊缓存, 这 将导致这个不存在的数据每次请求都要到数据库去查询, 造成缓存穿透

缓存穿透解决方案

- 缓存空对象:如果⼀个查询返回的数据为空(不管是数据不存在, 还是系统故障), 我们仍然把这个空结果进 ⾏缓存, 但它的过期时间会很短, 最长不超过五分钟

空值做了缓存, 意味着缓存中存了更多的键, 需要更多的内存空间, ⽐较有效的⽅法是针对这类数据设置⼀个 较短的过期时间, 让其⾃动剔除;  2. 缓存和存储的数据会有⼀段时间窗⼝的不⼀致, 可能会对业务有⼀定影响; 例如 过期时间设置为5分钟, 如果此时存储添加了这个数据, 那此段时间就会出现缓存和存储数据的不⼀致, 此时可以利⽤消息系统或者其他 方式清除掉缓存层中的空对象

- 布隆过滤器: 将所有可能存在的数据哈希到⼀个⾜够⼤的 bitmap 中, ⼀个⼀定不存在的数据会被这个 bitmap 拦截掉, 从⽽避免了对底层存储系统的查询压⼒
- 增强id的复杂度 避免被猜测id规律
- 做好数据的基础格式校验
- 加强用户权限校验
- 做好热点参数的限流

### 缓存击穿

缓存击穿也叫热点 Key 问题 就是一个被高并发访问并且缓存重建业务较为复杂的key突然失效了 无数的请求访问会在瞬间给数据库带来巨大的冲击

常见解决方案

- 互斥锁
- 逻辑过期

| 解决方案 | 优点                                   | 缺点                                     |
| -------- | -------------------------------------- | ---------------------------------------- |
| 互斥锁   | 没有额外的内存消耗 保证一致性 实现简单 | 线程需要等待 性能受到影响 可能有死锁风险 |
| 逻辑过期 | 线程无需等待 性能较好                  | 不保证一致性 有额外内存消耗 实现复杂     |

缓存穿透和缓存击穿的区别

- 缓存穿透是指缓存没有起到压力缓冲的作用
- 缓存击穿是指缓存失效瞬时的并发达到数据库

### 布隆过滤器

布隆过滤器是一种概率型数据库结构 由一个很长的二进制向量和一系列随机映射函数组成

布隆过滤器可以用于检索一个元素是否在一个集合中

优点是空间效率和查询时间都远远超过一般的算法 缺点是有一定的误识别率和删除困难

原理是当一个元素被加入到集合时 通过 k 个散列函数将这个元素映射为一个 m 位bit数组中的k个点并置为1

如果这些点中有任何一个0 则元素一定不存在 如果都是1 则可能存在

- 如果布隆过滤器认为值不存在 那么值一定不存在 无需查询缓存也无需查询数据库
- 对于极小概率的误判请求才会最终让非法 Key 的请求走到缓存或数据库

案例

- 分布式系统(Map-Reduce)

### redis 支持的数据类型有哪些

**推荐做法**: 理解Redis的五种基本数据类型及其使用场景

**基本数据类型**:

1. **String(字符串)**
   - 最基础的数据类型, 二进制安全
   - 最大512MB
   - 常用命令: SET、GET、INCR、DECR、APPEND
   - 使用场景: 缓存、计数器、分布式锁

2. **Hash(哈希)**
   - 键值对集合, 适合存储对象
   - 每个Hash可存储2^32-1个键值对
   - 常用命令: HSET、HGET、HGETALL、HDEL
   - 使用场景: 用户信息、商品信息等对象存储

3. **List(列表)**
   - 双向链表, 按插入顺序排序
   - 最多可存储2^32-1个元素
   - 常用命令: LPUSH、RPUSH、LPOP、RPOP、LRANGE
   - 使用场景: 消息队列、最新消息列表、文章列表

4. **Set(集合)**
   - 无序集合, 元素唯一
   - 支持交集、并集、差集操作
   - 常用命令: SADD、SMEMBERS、SINTER、SUNION
   - 使用场景: 标签、共同关注、去重

5. **Sorted Set(有序集合)**
   - 有序集合, 每个元素关联一个分数(score)
   - 按分数排序, 分数可重复
   - 常用命令: ZADD、ZRANGE、ZREVRANGE、ZRANK
   - 使用场景: 排行榜、延时队列、范围查询

**高级数据类型**:

6. **BitMap(位图)**
   - 基于String实现的位操作
   - 常用命令: SETBIT、GETBIT、BITCOUNT、BITOP
   - 使用场景: 用户签到、统计活跃用户、布隆过滤器

7. **HyperLogLog**
   - 用于基数统计, 误差率约0.81%
   - 常用命令: PFADD、PFCOUNT、PFMERGE
   - 使用场景: UV统计、独立访客数

8. **Geospatial(地理空间)**
   - 存储地理位置信息
   - 常用命令: GEOADD、GEODIST、GEORADIUS
   - 使用场景: 附近的人、距离计算

**为什么不推荐其他理解**:
- 避免将所有数据都用String存储, 应根据数据结构选择合适类型
- 理解每种类型的底层实现, 合理选择提升性能

### redis 支持的java客户端有哪些

Redisson、Jedis、lettuce

### jedis 和 redisson 有哪些区别

**推荐做法**: 根据项目需求选择合适的Redis客户端

**区别对比**:

| 特性 | Jedis | Redisson |
|------|-------|----------|
| **API风格** | 直接操作Redis命令 | 面向对象API |
| **连接管理** | 直连模式, 需要手动管理连接池 | 基于Netty的异步非阻塞 |
| **分布式功能** | 需要自己实现 | 内置分布式锁、限流器等 |
| **序列化** | 需要手动序列化 | 自动序列化 |
| **线程安全** | 非线程安全, 需使用连接池 | 线程安全 |
| **性能** | 同步阻塞IO | 异步非阻塞IO, 性能更好 |
| **学习成本** | 低(直接对应Redis命令) | 中(需要学习API) |

**Jedis示例**:
```java
// Jedis: 直接操作Redis命令
Jedis jedis = jedisPool.getResource();
try {
    jedis.set("key", "value");
    String value = jedis.get("key");
} finally {
    jedis.close();
}
```

**Redisson示例**:
```java
// Redisson: 面向对象API
RedissonClient redisson = Redisson.create(config);
RBucket<String> bucket = redisson.getBucket("key");
bucket.set("value");
String value = bucket.get();
```

**推荐选择**:
- **Jedis**: 简单场景, 直接操作Redis命令, 学习成本低
- **Redisson**: 复杂场景, 需要分布式锁、限流等高级功能, 性能要求高

**为什么不推荐其他做法**:
- 避免在高并发场景使用Jedis直连, 应使用连接池
- 避免重复造轮子, Redisson已实现分布式锁等功能

### 怎么保证缓存和数据库数据一致性

**推荐做法**: 采用Cache Aside模式, 先更新数据库再删除缓存, 配合延迟双删策略

**缓存更新策略**:

1. **Cache Aside模式(推荐)**
   - 读: 先查缓存, 未命中查数据库并写入缓存
   - 写: 先更新数据库, 再删除缓存
   ```java
   // 读操作
   public User getUser(Long id) {
       String key = "user:" + id;
       String userJson = redis.get(key);
       if (userJson != null) {
           return JSON.parseObject(userJson, User.class);
       }
       User user = userMapper.selectById(id);
       if (user != null) {
           redis.setex(key, 3600, JSON.toJSONString(user));
       }
       return user;
   }
   
   // 写操作
   @Transactional
   public void updateUser(User user) {
       // 1. 更新数据库
       userMapper.updateById(user);
       // 2. 删除缓存
       redis.del("user:" + user.getId());
   }
   ```

2. **延迟双删策略(解决并发问题)**
   ```java
   @Transactional
   public void updateUser(User user) {
       // 1. 先删除缓存
       redis.del("user:" + user.getId());
       // 2. 更新数据库
       userMapper.updateById(user);
       // 3. 延迟删除缓存(异步)
       CompletableFuture.runAsync(() -> {
           try {
               Thread.sleep(500); // 延迟500ms
               redis.del("user:" + user.getId());
           } catch (InterruptedException e) {
               Thread.currentThread().interrupt();
           }
       });
   }
   ```

3. **消息队列保证最终一致性**
   ```java
   // 更新数据库后发送消息
   @Transactional
   public void updateUser(User user) {
       userMapper.updateById(user);
       // 发送消息到MQ
       rabbitTemplate.convertAndSend("cache.update", "user:" + user.getId());
   }
   
   // 消费者删除缓存
   @RabbitListener(queues = "cache.update")
   public void handleCacheUpdate(String key) {
       redis.del(key);
   }
   ```

4. **使用Canal监听binlog**
   - Canal监听MySQL binlog
   - 自动删除或更新Redis缓存
   - 保证最终一致性

**为什么不推荐其他做法**:
- 避免先删除缓存再更新数据库, 可能导致脏读
- 避免更新缓存而非删除, 可能写入无效数据
- 避免忽略并发场景, 应使用延迟双删或消息队列

### redis 持久化有几种

**推荐做法**: 理解RDB和AOF两种持久化方式, 根据场景选择或组合使用

**持久化方式**:

1. **RDB(Redis Database)**
   - 快照方式, 定期将内存数据保存到磁盘
   - 文件格式: 二进制dump.rdb文件
   - 触发方式:
     - 手动: SAVE(阻塞)、BGSAVE(后台)
     - 自动: 配置save规则(如save 900 1)
   - 优点:
     - 文件小, 恢复速度快
     - 适合备份和灾难恢复
   - 缺点:
     - 可能丢失最后一次快照后的数据
     - 数据量大时fork子进程耗时

2. **AOF(Append Only File)**
   - 追加日志方式, 记录每个写操作
   - 文件格式: 文本文件, 记录Redis命令
   - 同步策略:
     - always: 每次写操作都同步(最安全, 性能最差)
     - everysec: 每秒同步一次(推荐, 平衡性能和安全性)
     - no: 由操作系统决定(性能最好, 可能丢失数据)
   - 优点:
     - 数据丢失少, 最多丢失1秒数据
     - AOF文件可读, 便于问题排查
   - 缺点:
     - 文件体积大
     - 恢复速度慢

**推荐配置**:
```properties
# RDB配置
save 900 1      # 900秒内至少1个key变化
save 300 10     # 300秒内至少10个key变化
save 60 10000   # 60秒内至少10000个key变化

# AOF配置
appendonly yes
appendfsync everysec  # 推荐使用everysec
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
```

**混合持久化(Redis 4.0+)**:
- RDB + AOF同时开启
- AOF文件包含RDB快照 + 增量AOF日志
- 兼顾RDB的快速恢复和AOF的数据完整性

**为什么不推荐其他做法**:
- 避免只使用RDB, 可能丢失较多数据
- 避免AOF使用always策略, 性能影响大, 推荐everysec

### redis 怎么实现分布式锁

**推荐做法**: 使用SET命令的NX和EX选项实现分布式锁, 或使用Redisson

**基础实现(SET NX EX)**:
```java
// 加锁
public boolean tryLock(String key, String value, long expireTime) {
    // SET key value NX EX expireTime
    // NX: 仅当key不存在时设置
    // EX: 设置过期时间(秒)
    String result = jedis.set(key, value, "NX", "EX", expireTime);
    return "OK".equals(result);
}

// 释放锁(需要判断value, 防止误删)
public boolean releaseLock(String key, String value) {
    String script = "if redis.call('get', KEYS[1]) == ARGV[1] then " +
                    "return redis.call('del', KEYS[1]) else return 0 end";
    Object result = jedis.eval(script, Collections.singletonList(key), 
                               Collections.singletonList(value));
    return Long.valueOf(1).equals(result);
}
```

**Redisson实现(推荐)**:
```java
// Redisson分布式锁
RLock lock = redisson.getLock("myLock");
try {
    // 尝试加锁, 最多等待100秒, 锁定后30秒自动解锁
    boolean res = lock.tryLock(100, 30, TimeUnit.SECONDS);
    if (res) {
        // 业务逻辑
    }
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
} finally {
    if (lock.isHeldByCurrentThread()) {
        lock.unlock();
    }
}
```

**关键要点**:
1. **原子性**: 使用SET NX EX保证加锁和设置过期时间的原子性
2. **唯一值**: 使用UUID等唯一值, 防止误删其他线程的锁
3. **过期时间**: 必须设置过期时间, 防止死锁
4. **释放锁**: 使用Lua脚本保证判断和删除的原子性

**为什么不推荐其他做法**:
- 避免使用SETNX + EXPIRE(非原子操作, 可能死锁)
- 避免不设置过期时间(可能导致死锁)
- 避免直接DEL删除锁(可能误删其他线程的锁)

### 如果有大量的请求进来怎么解决

**推荐做法**: 采用多级防护策略, 从接入层到应用层逐层限流

**解决方案**:

1. **接入层限流**
   - Nginx限流: limit_req模块限制请求速率
   - 网关限流: Spring Cloud Gateway、Sentinel限流
   ```nginx
   # Nginx限流配置
   limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
   limit_req zone=api_limit burst=20 nodelay;
   ```

2. **应用层限流**
   - 令牌桶算法: 控制请求速率
   - 漏桶算法: 平滑突发流量
   - 滑动窗口: 限制时间窗口内请求数
   ```java
   // 使用Guava RateLimiter
   RateLimiter rateLimiter = RateLimiter.create(10.0); // 每秒10个请求
   if (rateLimiter.tryAcquire()) {
       // 处理请求
   } else {
       // 拒绝请求
   }
   ```

3. **Redis限流**
   - 使用Redis实现分布式限流
   ```java
   // 滑动窗口限流
   public boolean isAllowed(String key, int limit, int window) {
       long now = System.currentTimeMillis();
       long windowStart = now - window * 1000;
       jedis.zremrangeByScore(key, 0, windowStart);
       long count = jedis.zcard(key);
       if (count < limit) {
           jedis.zadd(key, now, UUID.randomUUID().toString());
           jedis.expire(key, window);
           return true;
       }
       return false;
   }
   ```

4. **缓存预热**
   - 系统启动时预加载热点数据
   - 定时刷新缓存, 避免缓存过期

5. **降级策略**
   - 返回默认值或缓存数据
   - 熔断机制, 保护下游服务

6. **队列削峰**
   - 使用消息队列缓冲请求
   - 异步处理非实时业务

**为什么不推荐其他做法**:
- 避免单一限流策略, 应多层防护
- 避免限流过严导致正常请求被拒绝, 应合理设置阈值

### redis 怎么保证可用性

**推荐做法**: 采用主从复制、哨兵模式或集群模式保证高可用

**高可用方案**:

1. **主从复制(Master-Slave)**
   - 主节点负责写, 从节点负责读
   - 主节点故障需要手动切换
   - 适用场景: 读写分离, 数据备份
   ```bash
   # 配置从节点
   SLAVEOF master_ip master_port
   ```

2. **哨兵模式(Sentinel)(推荐)**
   - 监控主从节点健康状态
   - 自动故障转移
   - 自动通知客户端新的主节点地址
   ```bash
   # 启动哨兵
   redis-sentinel sentinel.conf
   ```
   ```properties
   # sentinel.conf配置
   sentinel monitor mymaster 127.0.0.1 6379 2
   sentinel down-after-milliseconds mymaster 5000
   sentinel failover-timeout mymaster 10000
   ```

3. **集群模式(Cluster)(推荐)**
   - 多主多从, 数据分片
   - 自动故障转移
   - 支持水平扩展
   ```bash
   # 创建集群
   redis-cli --cluster create \
     127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 \
     127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 \
     --cluster-replicas 1
   ```

4. **数据持久化**
   - RDB + AOF双重保障
   - 防止数据丢失

5. **客户端高可用**
   ```java
   // Jedis连接池配置
   JedisPoolConfig config = new JedisPoolConfig();
   config.setMaxTotal(100);
   config.setMaxIdle(20);
   config.setTestOnBorrow(true);
   
   // Redisson哨兵模式
   Config config = new Config();
   config.useSentinelServers()
       .addSentinelAddress("127.0.0.1:26379")
       .setMasterName("mymaster");
   ```

**高可用指标**:
- **RTO(恢复时间目标)**: 哨兵模式通常<30秒
- **RPO(数据恢复点目标)**: 取决于持久化策略
- **可用性**: 99.9%以上(年停机时间<8.76小时)

**为什么不推荐其他做法**:
- 避免单机部署, 无高可用保障
- 避免只使用主从复制, 故障需要手动切换
- 生产环境推荐使用哨兵或集群模式

### redis 分布式锁有什么缺陷

基于 setnx 实现的分布式锁存在以下的问题

- 不可重入 多个线程无法多次获取统一把锁
- 不可重试 获取锁只尝试一次就返回 没有重试机制
- 超时释放 锁超时释放虽然可以避免死锁 但是如果是业务执行耗时较长 也会导致所释放 存在安全隐患
- 主从一致性 如果 redis 提供了主从集群 主从同步存在延迟
- 锁未被释放
- B的锁被A给释放了
- 数据库事务超时
- 锁过期了, 业务还没执行完
- redis主从复制

### redis 如何做内存优化

**推荐做法**: 从数据结构、编码方式、过期策略等多维度优化内存

**内存优化策略**:

1. **合理选择数据类型**
   - 小数据用String, 对象用Hash
   - 避免使用String存储大对象, 应使用Hash分段存储
   ```java
   // 不推荐: 大对象用String
   jedis.set("user:1001", JSON.toJSONString(user)); // 序列化后可能很大
   
   // 推荐: 使用Hash
   jedis.hset("user:1001", "name", user.getName());
   jedis.hset("user:1001", "age", String.valueOf(user.getAge()));
   ```

2. **使用压缩编码**
   - Redis自动选择合适编码(ziplist、intset等)
   - Hash、List、Set、ZSet在元素少时使用压缩编码
   ```properties
   # 配置压缩编码阈值
   hash-max-ziplist-entries 512
   hash-max-ziplist-value 64
   list-max-ziplist-size -2
   set-max-intset-entries 512
   ```

3. **设置合理的过期时间**
   - 避免数据无限制增长
   - 使用EXPIRE设置过期时间

4. **使用对象共享池**
   - 0-9999的整数会共享, 减少内存占用
   ```properties
   # 配置共享对象池大小
   # 注意: 共享对象池会消耗CPU, 需权衡
   ```

5. **避免存储大Key**
   - 单个Key的value不要超过10KB
   - 大Key会导致阻塞、网络延迟等问题
   ```java
   // 不推荐: 大Key
   jedis.set("big:key", hugeString); // 可能几MB
   
   // 推荐: 拆分大Key
   for (int i = 0; i < chunks.length; i++) {
       jedis.set("big:key:" + i, chunks[i]);
   }
   ```

6. **使用BitMap替代Set**
   - 对于布尔值场景, BitMap更省内存
   ```java
   // Set存储用户ID: 每个ID占用更多内存
   jedis.sadd("active:users", "1001", "1002");
   
   // BitMap存储: 每个用户只占1bit
   jedis.setbit("active:users", 1001, true);
   ```

7. **定期清理无用数据**
   - 使用SCAN命令定期清理过期Key
   - 监控内存使用情况

**内存分析工具**:
```bash
# 查看内存使用情况
redis-cli --bigkeys

# 分析内存使用
redis-cli MEMORY USAGE key

# 查看内存统计
redis-cli INFO memory
```

**为什么不推荐其他做法**:
- 避免所有数据都用String存储, 应根据场景选择合适类型
- 避免不设置过期时间, 导致内存无限增长

### redis 淘汰策略有哪些

### redis 常见的性能问题有哪些 怎么解决

**推荐做法**: 识别常见性能问题, 采用针对性优化方案

**常见性能问题及解决方案**:

1. **慢查询**
   - **问题**: 执行时间过长的命令阻塞Redis
   - **解决**:
     ```bash
     # 设置慢查询阈值(微秒)
     CONFIG SET slowlog-log-slower-than 10000
     # 查看慢查询日志
     SLOWLOG GET 10
     ```
   - **优化**: 避免使用KEYS、SMEMBERS等O(N)命令, 使用SCAN替代

2. **大Key问题**
   - **问题**: 单个Key过大导致阻塞、网络延迟
   - **解决**:
     ```bash
     # 查找大Key
     redis-cli --bigkeys
     # 分析Key大小
     MEMORY USAGE key
     ```
   - **优化**: 拆分大Key, 使用Hash分段存储

3. **内存碎片**
   - **问题**: 内存使用率低但实际内存不足
   - **解决**:
     ```bash
     # 查看内存碎片率
     INFO memory
     # 内存碎片率 = used_memory_rss / used_memory
     # 如果 > 1.5, 考虑重启或清理
     ```

4. **频繁的Key过期**
   - **问题**: 大量Key同时过期导致阻塞
   - **解决**: 给过期时间添加随机值, 避免集中过期
     ```java
     // 不推荐: 固定过期时间
     jedis.setex("key", 3600, "value");
     
     // 推荐: 添加随机值
     int expireTime = 3600 + new Random().nextInt(600); // 3600-4200秒
     jedis.setex("key", expireTime, "value");
     ```

5. **网络延迟**
   - **问题**: 客户端与Redis服务器网络延迟高
   - **解决**: 
     - 使用连接池减少连接开销
     - 使用Pipeline批量操作
     ```java
     // Pipeline批量操作
     Pipeline pipeline = jedis.pipelined();
     for (int i = 0; i < 1000; i++) {
         pipeline.set("key" + i, "value" + i);
     }
     pipeline.sync();
     ```

6. **持久化阻塞**
   - **问题**: RDB/AOF持久化导致阻塞
   - **解决**:
     - RDB使用BGSAVE而非SAVE
     - AOF使用everysec而非always
     - 避免在高峰期进行持久化

7. **CPU使用率高**
   - **问题**: 复杂命令或大量连接导致CPU高
   - **解决**:
     - 优化命令, 避免复杂计算
     - 限制连接数
     - 使用读写分离

**性能监控**:
```bash
# 实时监控命令
redis-cli --stat

# 查看性能指标
INFO stats
INFO commandstats
```

**为什么不推荐其他做法**:
- 避免使用KEYS命令在生产环境, 应使用SCAN
- 避免频繁创建连接, 应使用连接池

### redis删除过期key的算法

**推荐做法**: 理解Redis的三种过期Key删除策略, 合理配置保证性能

**过期Key删除策略**:

1. **惰性删除(Lazy Expiration)**
   - **时机**: 访问Key时检查是否过期
   - **优点**: CPU友好, 只删除被访问的过期Key
   - **缺点**: 过期Key不被访问会一直占用内存
   ```java
   // 访问Key时检查
   String value = jedis.get("key");
   // 如果key已过期, Redis会删除它并返回null
   ```

2. **定期删除(Periodic Expiration)**
   - **时机**: 每隔一定时间扫描过期Key
   - **流程**:
     - 随机抽取20个Key检查
     - 删除其中过期的Key
     - 如果过期Key比例>25%, 继续扫描
   - **优点**: 平衡CPU和内存
   - **配置**: 默认每秒执行10次(可通过hz配置)
   ```properties
   # 配置定期删除频率(1-500)
   hz 10
   ```

3. **内存不够时删除**
   - **时机**: 执行写入命令时, 内存不足触发淘汰策略
   - **流程**: 按照配置的淘汰策略删除Key
   - **淘汰策略**: 见"redis 淘汰策略有哪些"

**三种策略配合**:
- **惰性删除**: 保证访问时立即删除过期Key
- **定期删除**: 定期清理过期Key, 减少内存占用
- **内存淘汰**: 内存不足时强制删除, 保证服务可用

**为什么不推荐其他理解**:
- 避免只依赖惰性删除, 过期Key可能长期占用内存
- 避免hz设置过大, 影响Redis性能

当执行写入命令时, 如果发现内存不够, 那么就会按照配置的淘汰策略清理内存

淘汰策略一般有6种, Redis4.0版本后又增加了2种, 主要由分为三类

第一类 不处理, 等报错(默认的配置)noeviction, 发现内存不够时, 不删除key, 执行写入命令时直接返回错误信息(Redis默认的配置就是noeviction)

第二类 从所有结果集中的key中挑选, 进行淘汰allkeys-random 就是从所有的key中随机挑选key, 进行淘汰; allkeys-lru 就是从所有的key中挑选最近使用时间距离现在最远的key, 进行淘汰; allkeys-lfu 就是从所有的key中挑选使用频率最低的key, 进行淘汰(这是Redis 4.0版本后新增的策略)

第三类 从设置了过期时间的key中挑选, 进行淘汰, 这种就是从设置了expires过期时间的结果集中选出一部分key淘汰, 挑选的算法有

-  volatile-random 从设置了过期时间的结果集中随机挑选key删除
- volatile-lru 从设置了过期时间的结果集中挑选上次使用时间距离现在最久的key开始删除
-  volatile-ttl 从设置了过期时间的结果集中挑选可存活时间最短的key开始删除(也就是从哪些快要过期的key中先删除)
- volatile-lfu 从过期时间的结果集中选择使用频率最低的key开始删除(这是Redis 4.0版本后新增的策略)

### epoll和poll的区别

- select模型: 使用的是数组来存储Socket连接文件描述符, 容量是固定的, 需要通过轮询来判断是否发生了IO事件(同步)
- poll模型: 使用的是链表来储存Socket连接文件描述符, 容量是不固定的, 同样需要通过轮询来判断是否发生了IO事件(同步)
- epoll模型: epoll和poll是完全不同的 epoll是一种事件通知模型(异步)

### Redis集群策略

- 主从模式: 这种模式比较简单, 主库可以读写, 并且会和从库进行数据同步; 这种模式下客户端直接连接主库或者某个从库, 但是当主库或者从库宕机后, 客户端需要手动修改IP, 另外, 这种模式也比较难进行扩容, 整个集群所能储存的数据收到某台机器的内存容量的限制, 所以不可能支持特大数据量
- 哨兵模式: 这种模式在主从的基础上新增了哨兵节点, 当主库节点宕机后, 哨兵会发现主库节点宕机, 然后会在从库中选择一个作为新的主库; 另外哨兵也可以做集群, 从而可以保证当某一个哨兵节点宕机后 , 还有其他哨兵节点可以继续工作, 这种模式可以比较好的保证Redis集群的高可用, 但是仍然不能很好解决Redis的容量上限问题
- Cluster模式: Cluster模式是用的比较多的模式 它支持多主多从, 这种模式会按照key进行槽位的分配 可以使得不同的key分散到不同的主节点上, 利用这种模式可以使得整个集群支持更大的数据容量, 同时每个主节点可以拥有自己的多个从节点; 如果该主节点宕机, 会从它的从节点中选举一个新的主节点

### 在集群模式下, Redis 的 key 是如何寻址的

**推荐做法**: 理解Redis Cluster的Hash Slot寻址机制

**Redis Cluster寻址机制**:

1. **Hash Slot(哈希槽)**
   - Redis Cluster使用16384个槽位(0-16383)
   - 每个节点负责一部分槽位
   - Key通过CRC16算法计算hash值, 然后对16384取模得到slot

2. **寻址流程**:
   ```
   Key → CRC16(key) → CRC16(key) % 16384 → Slot → Node
   ```

3. **槽位分配**:
   ```bash
   # 查看槽位分配
   CLUSTER SLOTS
   # 输出示例:
   # 1) 1) (integer) 0
   #    2) (integer) 5460
   #    3) 1) "127.0.0.1"
   #       2) (integer) 7000
   #    4) 1) "127.0.0.1"
   #       2) (integer) 7001
   ```

4. **客户端寻址**:
   - 客户端连接任意节点
   - 如果Key不在当前节点, 返回MOVED错误和正确节点地址
   - 客户端更新路由表, 重定向到正确节点

5. **示例**:
   ```java
   // Key "user:1001"的寻址过程
   // 1. CRC16("user:1001") = 12345
   // 2. 12345 % 16384 = 12345
   // 3. Slot 12345 可能分配给 Node A
   // 4. 客户端连接Node A操作该Key
   ```

**为什么不推荐其他理解**:
- 避免认为Redis Cluster使用一致性Hash, 实际使用Hash Slot
- 理解Hash Slot的优势: 便于数据迁移和负载均衡

### 分布式寻址都有哪些算法; 说一下一致性 hash

**推荐做法**: 理解不同分布式寻址算法的优缺点, 根据场景选择

**分布式寻址算法**:

1. **Hash算法(取模)**
   - **原理**: `hash(key) % node_count`
   - **优点**: 实现简单, 分布均匀
   - **缺点**: 
     - 节点增减需要重新计算所有Key
     - 大量缓存重建, 影响性能
   - **适用场景**: 节点数量固定不变

2. **一致性Hash算法**
   - **原理**: 
     - 将Hash值空间组织成虚拟的圆环(0-2^32-1)
     - 节点和Key都映射到环上
     - Key顺时针找到的第一个节点即为目标节点
   - **优点**:
     - 节点增减只影响相邻节点
     - 缓存迁移量小
   - **缺点**:
     - 节点分布不均可能导致数据倾斜
     - 需要虚拟节点解决负载均衡问题
   - **虚拟节点**:
     ```java
     // 为每个物理节点创建多个虚拟节点
     // 虚拟节点数越多, 分布越均匀
     for (int i = 0; i < virtualNodeCount; i++) {
         String virtualNode = node + "#" + i;
         int hash = hash(virtualNode);
         ring.put(hash, node);
     }
     ```
   - **适用场景**: Memcached、分布式缓存

3. **Hash Slot算法(Redis Cluster)**
   - **原理**: 
     - 固定16384个槽位
     - Key通过CRC16计算后对16384取模得到slot
     - 槽位可以动态分配给不同节点
   - **优点**:
     - 槽位迁移粒度小, 只迁移部分槽位
     - 负载均衡好, 可以手动调整槽位分布
     - 支持动态扩容缩容
   - **缺点**:
     - 实现复杂
     - 需要客户端支持重定向
   - **适用场景**: Redis Cluster

**一致性Hash详细说明**:
```java
public class ConsistentHash {
    private final SortedMap<Integer, String> ring = new TreeMap<>();
    private final int virtualNodeCount;
    
    public ConsistentHash(int virtualNodeCount) {
        this.virtualNodeCount = virtualNodeCount;
    }
    
    // 添加节点
    public void addNode(String node) {
        for (int i = 0; i < virtualNodeCount; i++) {
            String virtualNode = node + "#" + i;
            int hash = hash(virtualNode);
            ring.put(hash, node);
        }
    }
    
    // 查找节点
    public String getNode(String key) {
        if (ring.isEmpty()) {
            return null;
        }
        int hash = hash(key);
        SortedMap<Integer, String> tailMap = ring.tailMap(hash);
        if (tailMap.isEmpty()) {
            return ring.get(ring.firstKey()); // 环首节点
        }
        return tailMap.get(tailMap.firstKey());
    }
    
    private int hash(String key) {
        return key.hashCode();
    }
}
```

**算法对比**:

| 算法 | 缓存重建量 | 负载均衡 | 实现复杂度 | 适用场景 |
|------|-----------|---------|-----------|---------|
| Hash取模 | 高(全部重建) | 好 | 低 | 节点固定 |
| 一致性Hash | 低(部分重建) | 需虚拟节点 | 中 | 分布式缓存 |
| Hash Slot | 低(槽位迁移) | 好 | 高 | Redis Cluster |

**为什么不推荐其他做法**:
- 避免在节点经常变化的场景使用Hash取模, 应使用一致性Hash
- 避免一致性Hash不使用虚拟节点, 会导致负载不均

### Redis 的并发竞争问题是什么; 如何解决这个问题

**推荐做法**: 理解并发竞争问题的本质, 使用分布式锁或CAS机制解决

**并发竞争问题**:

1. **问题描述**
   - 多个客户端同时修改同一个Key
   - 导致数据不一致或覆盖问题
   - 典型场景: 库存扣减、计数器递增

2. **问题示例**:
   ```java
   // 线程A和B同时执行
   // 线程A: value = get("count"); value++; set("count", value);
   // 线程B: value = get("count"); value++; set("count", value);
   // 结果: 期望+2, 实际只+1(丢失更新)
   ```

**解决方案**:

1. **分布式锁(推荐)**
   ```java
   // 使用分布式锁保证原子性
   RLock lock = redisson.getLock("count:lock");
   try {
       lock.lock();
       int value = Integer.parseInt(jedis.get("count"));
       value++;
       jedis.set("count", String.valueOf(value));
   } finally {
       lock.unlock();
   }
   ```

2. **Redis原子命令**
   ```java
   // 使用INCR原子命令
   Long result = jedis.incr("count");
   // 或使用INCRBY
   Long result = jedis.incrBy("count", 1);
   ```

3. **Lua脚本(推荐, 保证原子性)**
   ```java
   String script = "local current = redis.call('get', KEYS[1]) " +
                   "if current == false then " +
                   "  current = 0 " +
                   "end " +
                   "local new = current + ARGV[1] " +
                   "redis.call('set', KEYS[1], new) " +
                   "return new";
   Object result = jedis.eval(script, 
                              Collections.singletonList("count"),
                              Collections.singletonList("1"));
   ```

4. **WATCH + MULTI(乐观锁)**
   ```java
   // 使用WATCH监控Key变化
   jedis.watch("count");
   Transaction tx = jedis.multi();
   tx.incr("count");
   List<Object> results = tx.exec();
   if (results == null) {
       // 事务失败, Key被其他客户端修改
       // 重试或放弃
   }
   ```

5. **版本号机制**
   ```java
   // 使用版本号控制并发
   String versionKey = "count:version";
   String dataKey = "count:data";
   
   jedis.watch(versionKey);
   int version = Integer.parseInt(jedis.get(versionKey));
   Transaction tx = jedis.multi();
   tx.incr(versionKey);
   tx.set(dataKey, newValue);
   List<Object> results = tx.exec();
   ```

**推荐方案选择**:
- **简单操作**: 使用原子命令(INCR、DECR等)
- **复杂操作**: 使用Lua脚本
- **需要重试**: 使用分布式锁
- **读多写少**: 使用WATCH + MULTI乐观锁

**为什么不推荐其他做法**:
- 避免在Redis中实现复杂的业务逻辑, 应使用Lua脚本保证原子性
- 避免使用WATCH + MULTI处理高并发场景, 失败率高, 应使用分布式锁

### 了解 Redis 事务的 CAS 方案吗

**推荐做法**: 理解Redis事务的CAS(Compare-And-Swap)机制, 使用WATCH实现乐观锁

**Redis事务CAS机制**:

1. **WATCH命令**
   - 监控一个或多个Key
   - 如果被监控的Key在EXEC执行前被修改, 事务将失败
   - 实现乐观锁机制

2. **事务执行流程**:
   ```java
   // 1. WATCH监控Key
   jedis.watch("balance");
   
   // 2. 获取当前值
   int balance = Integer.parseInt(jedis.get("balance"));
   
   // 3. 开启事务
   Transaction tx = jedis.multi();
   
   // 4. 在事务中执行命令
   if (balance >= 100) {
       tx.decrBy("balance", 100);
   }
   
   // 5. 执行事务
   List<Object> results = tx.exec();
   
   // 6. 判断事务是否成功
   if (results == null) {
       // 事务失败, Key被其他客户端修改
       // 需要重试
   } else {
       // 事务成功
   }
   ```

3. **CAS实现示例**:
   ```java
   public boolean cas(String key, String expectedValue, String newValue) {
       jedis.watch(key);
       String currentValue = jedis.get(key);
       
       if (!expectedValue.equals(currentValue)) {
           jedis.unwatch();
           return false; // 值不匹配, CAS失败
       }
       
       Transaction tx = jedis.multi();
       tx.set(key, newValue);
       List<Object> results = tx.exec();
       
       return results != null; // 返回CAS是否成功
   }
   ```

4. **CAS vs 分布式锁**:
   | 特性 | CAS(乐观锁) | 分布式锁(悲观锁) |
   |------|-----------|----------------|
   | **冲突检测** | 执行时检测 | 执行前获取锁 |
   | **性能** | 无锁等待, 性能好 | 需要等待锁, 性能较差 |
   | **适用场景** | 读多写少, 冲突少 | 写多, 冲突频繁 |
   | **失败处理** | 需要重试机制 | 阻塞等待 |

5. **实际应用场景**:
   ```java
   // 账户余额扣减(使用CAS)
   public boolean deductBalance(String userId, int amount) {
       String key = "balance:" + userId;
       int retryCount = 0;
       int maxRetries = 3;
       
       while (retryCount < maxRetries) {
           jedis.watch(key);
           int balance = Integer.parseInt(jedis.get(key));
           
           if (balance < amount) {
               jedis.unwatch();
               return false; // 余额不足
           }
           
           Transaction tx = jedis.multi();
           tx.decrBy(key, amount);
           List<Object> results = tx.exec();
           
           if (results != null) {
               return true; // CAS成功
           }
           
           retryCount++; // CAS失败, 重试
           try {
               Thread.sleep(10); // 短暂等待后重试
           } catch (InterruptedException e) {
               Thread.currentThread().interrupt();
               return false;
           }
       }
       
       return false; // 重试次数用完
   }
   ```

**为什么不推荐其他做法**:
- 避免在高并发写场景使用CAS, 失败率高, 应使用分布式锁
- 避免CAS无限重试, 应设置最大重试次数
- 避免WATCH过多Key, 影响性能

### zset底层; 为什么底层要同时使用哈希表+跳表的结构

**推荐做法**: 理解ZSet的双重数据结构设计, 兼顾查询性能和范围查询

**ZSet底层结构**:

1. **双重数据结构**
   - **Hash表**: 存储member到score的映射, O(1)时间复杂度查询score
   - **跳表(SkipList)**: 按score排序存储member, 支持范围查询和排名操作

2. **为什么需要两种结构**:
   ```java
   // Hash表: 快速查询member的score
   ZSCORE key member  // O(1) - 使用Hash表
   
   // 跳表: 支持范围查询和排名
   ZRANGE key start stop  // O(log N) - 使用跳表
   ZRANK key member        // O(log N) - 使用跳表
   ```

3. **数据结构对比**:
   | 操作 | 只用Hash表 | 只用跳表 | Hash+跳表 |
   |------|-----------|---------|----------|
   | **查询score** | O(1) ✅ | O(log N) | O(1) ✅ |
   | **范围查询** | O(N) ❌ | O(log N) ✅ | O(log N) ✅ |
   | **排名查询** | O(N) ❌ | O(log N) ✅ | O(log N) ✅ |
   | **插入/删除** | O(1) ✅ | O(log N) | O(log N) |

4. **内存占用**:
   - Hash表: 存储member和score的映射
   - 跳表: 存储member、score和指针
   - 虽然占用更多内存, 但换来了更好的查询性能

5. **实际应用**:
   ```java
   // 排行榜场景
   // 1. 查询用户分数(使用Hash表)
   Double score = jedis.zscore("leaderboard", "user:1001");
   
   // 2. 获取Top 10(使用跳表)
   Set<String> top10 = jedis.zrevrange("leaderboard", 0, 9);
   
   // 3. 查询用户排名(使用跳表)
   Long rank = jedis.zrevrank("leaderboard", "user:1001");
   ```

**为什么不推荐其他做法**:
- 避免只使用Hash表, 无法高效支持范围查询和排名
- 避免只使用跳表, 查询score性能较差
- 理解空间换时间的设计思想

### 讲一下跳表的结构

**推荐做法**: 理解跳表的多层链表结构, 掌握其查询和插入原理

**跳表(SkipList)结构**:

1. **基本结构**
   - 多层有序链表
   - 底层是完整的有序链表(Level 0)
   - 上层是索引层, 节点数量逐层减半
   - 每个节点包含: 数据、分数(score)、多层指针数组

2. **结构示意图**:
   ```
   Level 3:  Head ──────────────────────────────> NULL
   Level 2:  Head ──────────> Node3 ──────────> NULL
   Level 1:  Head ──> Node2 ──> Node3 ──> Node5 ─> NULL
   Level 0:  Head ──> Node1 ──> Node2 ──> Node3 ──> Node4 ──> Node5 ──> NULL
             (1,10)  (2,20)   (3,30)   (4,40)   (5,50)
   ```

3. **节点结构**:
   ```java
   class SkipListNode {
       String member;           // 成员
       double score;            // 分数
       SkipListNode[] forward;  // 每层的下一个节点指针
       SkipListNode backward;   // 后退指针(用于反向遍历)
   }
   ```

4. **查询过程**:
   ```java
   // 查询score=30的节点
   // 1. 从最高层开始, 向右查找
   // 2. 如果下一个节点的score > 30, 向下移动一层
   // 3. 如果下一个节点的score <= 30, 向右移动
   // 4. 重复直到找到或到达底层
   
   // 时间复杂度: O(log N)
   ```

5. **插入过程**:
   ```java
   // 插入新节点(member="new", score=35)
   // 1. 找到插入位置(每层都要记录前驱节点)
   // 2. 随机生成层数(概率为0.5的几何分布)
   // 3. 创建新节点, 设置各层指针
   // 4. 更新前驱节点的指针指向新节点
   
   // 时间复杂度: O(log N)
   ```

6. **层数生成**:
   ```java
   // Redis跳表层数生成算法
   int randomLevel() {
       int level = 1;
       // 50%概率增加一层
       while (Math.random() < 0.5 && level < MAX_LEVEL) {
           level++;
       }
       return level;
   }
   // 期望层数约为log N
   ```

7. **优势**:
   - **查询效率**: O(log N), 接近平衡二叉树
   - **实现简单**: 比红黑树、AVL树简单
   - **范围查询**: 支持高效的范围查询
   - **插入删除**: O(log N), 无需旋转操作

8. **与平衡树对比**:
   | 特性 | 跳表 | 红黑树 |
   |------|------|--------|
   | **查询** | O(log N) | O(log N) |
   | **插入** | O(log N) | O(log N) |
   | **范围查询** | O(log N) | O(log N) |
   | **实现复杂度** | 简单 | 复杂 |
   | **内存占用** | 较多(多层指针) | 较少 |

**为什么不推荐其他理解**:
- 避免认为跳表是简单的链表, 它是多层索引结构
- 理解跳表通过空间换时间提升查询性能

### redis怎么保证和数据库数据一致性

**推荐做法**: 采用Cache Aside模式, 先更新数据库再删除缓存, 配合延迟双删策略

(此问题已在前面"怎么保证缓存和数据库数据一致性"部分详细说明, 请参考该部分)

### redis缓存到了过期时间是怎么删除缓存的

**推荐做法**: 理解Redis的三种过期Key删除策略

(此问题已在前面"redis删除过期key的算法"部分详细说明, 请参考该部分)

### 了解redis的哨兵模式吗

**推荐做法**: 理解哨兵模式的作用和配置, 用于Redis高可用

**哨兵模式(Sentinel)**:

1. **作用**
   - 监控主从节点健康状态
   - 自动故障转移(主节点故障时选举新主节点)
   - 自动通知客户端新的主节点地址
   - 配置提供者(客户端通过哨兵获取主节点地址)

2. **架构**:
   ```
   主节点(Master)
      ↓
   从节点1(Slave1) ← 哨兵1(Sentinel1)
   从节点2(Slave2) ← 哨兵2(Sentinel2)
   从节点3(Slave3) ← 哨兵3(Sentinel3)
   ```

3. **配置示例**:
   ```properties
   # sentinel.conf
   # 监控主节点 mymaster, 至少2个哨兵认为主节点下线才进行故障转移
   sentinel monitor mymaster 127.0.0.1 6379 2
   
   # 主节点下线判定时间(毫秒)
   sentinel down-after-milliseconds mymaster 5000
   
   # 故障转移超时时间(毫秒)
   sentinel failover-timeout mymaster 10000
   
   # 故障转移时, 最多有多少个从节点同时对新的主节点进行同步
   sentinel parallel-syncs mymaster 1
   ```

4. **故障转移流程**:
   ```
   1. 哨兵检测到主节点下线
   2. 多个哨兵确认主节点下线(主观下线 → 客观下线)
   3. 选举领头哨兵(Leader Sentinel)
   4. 领头哨兵从从节点中选择新主节点
   5. 将其他从节点指向新主节点
   6. 通知客户端新的主节点地址
   ```

5. **客户端连接**:
   ```java
   // Jedis连接哨兵
   Set<String> sentinels = new HashSet<>();
   sentinels.add("127.0.0.1:26379");
   sentinels.add("127.0.0.1:26380");
   sentinels.add("127.0.0.1:26381");
   
   JedisSentinelPool pool = new JedisSentinelPool("mymaster", sentinels);
   Jedis jedis = pool.getResource();
   ```

**为什么不推荐其他做法**:
- 避免单机部署, 无高可用保障
- 避免只使用主从复制, 故障需要手动切换

### redis 的sentinel原理能介绍一下么

**推荐做法**: 深入理解Sentinel的工作原理和实现机制

**Sentinel原理**:

1. **监控机制**
   - 每个Sentinel每秒向主节点、从节点、其他Sentinel发送PING命令
   - 如果超过down-after-milliseconds未收到有效回复, 标记为"主观下线"
   - 当足够数量的Sentinel(quorum)都认为主节点下线, 标记为"客观下线"

2. **故障转移流程**:
   ```
   步骤1: 主观下线(SDOWN)
   - Sentinel检测到主节点无响应
   - 标记主节点为"主观下线"
   
   步骤2: 客观下线(ODOWN)
   - 询问其他Sentinel是否也认为主节点下线
   - 如果quorum个Sentinel都认为下线, 标记为"客观下线"
   
   步骤3: 选举领头Sentinel
   - 使用Raft算法选举领头Sentinel
   - 只有领头Sentinel才能执行故障转移
   
   步骤4: 选择新主节点
   - 从从节点中选择:
     * 优先级高的(redis.conf中slave-priority)
     * 复制偏移量大的(数据最新)
     * 运行ID最小的(字典序)
   
   步骤5: 故障转移
   - 将选中的从节点升级为主节点
   - 将其他从节点指向新主节点
   - 将旧主节点(如果恢复)设置为新主节点的从节点
   
   步骤6: 通知客户端
   - 通过发布订阅通知客户端主节点变更
   - 客户端重新连接新主节点
   ```

3. **Raft算法选举**:
   ```java
   // Sentinel选举领头Sentinel的过程
   // 1. 每个Sentinel都可以发起选举
   // 2. 收到选举请求的Sentinel, 如果还没投票, 就投票给第一个请求者
   // 3. 如果某个Sentinel获得超过半数投票, 成为领头
   // 4. 如果选举超时, 重新发起选举
   ```

4. **发布订阅机制**:
   ```bash
   # Sentinel通过发布订阅通知客户端
   # 频道: __sentinel__:hello
   # 消息格式: 
   # <ip>,<port>,<runid>,<current_epoch>,<master_name>,<ip>,<port>,<master_epoch>
   ```

5. **配置纪元(Config Epoch)**:
   - 每次故障转移都会增加配置纪元
   - 用于区分不同的故障转移操作
   - 保证配置的唯一性

**为什么不推荐其他理解**:
- 避免认为单个Sentinel就能完成故障转移, 需要多个Sentinel达成共识
- 理解Sentinel使用Raft算法保证一致性

### 知道redis里面可以设置到期时间吗; 底层是怎么实现的

**推荐做法**: 理解Redis过期时间的设置方式和底层存储机制

**设置过期时间**:

1. **命令**:
   ```bash
   # 设置Key的过期时间(秒)
   EXPIRE key 3600
   
   # 设置Key的过期时间(毫秒)
   PEXPIRE key 3600000
   
   # 设置Key在指定时间戳过期(秒)
   EXPIREAT key 1640995200
   
   # 设置Key在指定时间戳过期(毫秒)
   PEXPIREAT key 1640995200000
   
   # 设置Key的同时设置过期时间
   SET key value EX 3600
   SET key value PX 3600000
   ```

2. **底层实现**:
   - Redis使用**过期字典(expires字典)**存储Key的过期时间
   - 过期字典的Key是指向键空间中的某个键对象的指针
   - 过期字典的Value是一个long long类型的整数, 存储过期时间戳(毫秒)

3. **数据结构**:
   ```c
   // Redis数据库结构
   typedef struct redisDb {
       dict *dict;              // 键空间, 存储所有Key-Value
       dict *expires;           // 过期字典, 存储Key的过期时间
       // ... 其他字段
   } redisDb;
   
   // 过期字典存储示例
   expires["user:1001"] = 1640995200000  // 过期时间戳
   ```

4. **过期时间检查**:
   ```c
   // 检查Key是否过期
   int keyIsExpired(redisDb *db, robj *key) {
       // 1. 从过期字典中获取过期时间
       long long when = getExpire(db, key);
       
       // 2. 如果没有设置过期时间, 返回0(未过期)
       if (when < 0) return 0;
       
       // 3. 如果当前时间大于过期时间, 返回1(已过期)
       if (mstime() > when) return 1;
       
       return 0;
   }
   ```

5. **过期Key删除**:
   - 惰性删除: 访问Key时检查是否过期
   - 定期删除: 定期扫描过期字典, 删除过期Key
   - 详见"redis删除过期key的算法"

6. **持久化过期时间**:
   - **RDB**: 保存Key时会保存过期时间, 加载时恢复
   - **AOF**: 记录EXPIRE命令, 重启时重新设置过期时间

**为什么不推荐其他理解**:
- 避免认为过期时间存储在Key-Value中, 实际存储在独立的过期字典
- 理解过期字典使用指针指向键空间, 节省内存

### 缓存更新策略

|          | 内存淘汰                                                     | 超时剔除                                                    | 主动更新                                 |
| -------- | ------------------------------------------------------------ | ----------------------------------------------------------- | ---------------------------------------- |
| 说明     | 不用自己维护 利用 redis 的内存淘汰机制 当内存不足时自动淘汰部分数据 下次查询时更新缓存 | 给缓存数据添加ttl时间 到期后自动删除缓存 下次查询时更新缓存 | 编写业务逻辑 在修改数据库中同时 更新缓存 |
| 一致性   | 差                                                           | 一般                                                        | 好                                       |
| 维护成本 | 无                                                           | 低                                                          | 高                                       |

主动更新策略

- 旁路缓存 由缓存的调用者 在更新数据库的同时更新缓存
- write through pattern 缓存和数据库整合为一个服务 由服务来维护一致性
- write behind caching 调用者只操作缓存 由其他线程异步的将缓存数据持久化到数据库 保证一致性

旁路缓存 有三个问题需要考虑

- 删除缓存还是更新缓存
  - 更新缓存 每次更新数据库都更新缓存 无效写操作较多
  - 删除缓存 更新数据库时让缓存失效 查询时再更新缓存
- 如何保证缓存和数据库的操作的原子性
  - 单体系统 将缓存与数据库放在一个事务
  - 分布式系统 利用TCC 等分布式事务方案
- 先操作缓存还是先操作数据库
  - 先删除缓存 再操作数据库
  - 先操作数据库 再删除缓存

先删除缓存 再操作数据库

- 正常情况  线程一 删除缓存 更新数据库 然后 线程二查询缓存 未命中 查询数据库 写入缓存
- 不正常 线程一 先删除缓存 线程二 查询缓存未命中 查询数据库 缓存中写入旧数据 然后线程一再更新数据库 数据库和缓存中的数据不一致

先操作数据库 再删除缓存

- 正常情况 线程一先更新数据库 再删除缓存 然后线程二 查询缓存未命中 查询数据库 写入缓存
- 不正常情况 线程一查询缓存 刚好缓存过期 未命中 于是查询数据库  线程二开始更新数据库  再删除缓存 然后线程一再写入缓存 此时数据库和缓存的数据不一致(解决 延迟双删策略)

 延迟双删策略

更新数据库后 延迟几微妙(毫秒) 再删除缓存

## 并发

### 线程池有哪些状态

**推荐做法**: 理解线程池的5种状态及其转换关系

**线程池状态**:

1. **RUNNING(运行中)**
   - 状态值: -1 << COUNT_BITS (即高3位为111)
   - 说明: 正常接受新任务, 处理队列中的任务
   - 转换: 初始状态

2. **SHUTDOWN(关闭)**
   - 状态值: 0 << COUNT_BITS (即高3位为000)
   - 说明: 不接受新任务, 但处理队列中的任务
   - 转换: 调用shutdown()方法

3. **STOP(停止)**
   - 状态值: 1 << COUNT_BITS (即高3位为001)
   - 说明: 不接受新任务, 不处理队列中的任务, 中断正在执行的任务
   - 转换: 调用shutdownNow()方法

4. **TIDYING(整理中)**
   - 状态值: 2 << COUNT_BITS (即高3位为010)
   - 说明: 所有任务已终止, workerCount为0, 线程池即将进入TERMINATED状态
   - 转换: 当线程池和队列都为空时自动转换

5. **TERMINATED(终止)**
   - 状态值: 3 << COUNT_BITS (即高3位为011)
   - 说明: terminated()方法执行完成
   - 转换: 从TIDYING状态转换而来

**状态转换图**:
```
RUNNING → shutdown() → SHUTDOWN → 队列和线程池为空 → TIDYING → terminated() → TERMINATED
  ↓
shutdownNow() → STOP → 线程池为空 → TIDYING → terminated() → TERMINATED
```

**源码实现**:
```java
// ThreadPoolExecutor中的状态定义
private static final int RUNNING    = -1 << COUNT_BITS; // 111
private static final int SHUTDOWN   =  0 << COUNT_BITS; // 000
private static final int STOP       =  1 << COUNT_BITS; // 001
private static final int TIDYING    =  2 << COUNT_BITS; // 010
private static final int TERMINATED =  3 << COUNT_BITS; // 011

// 状态存储在一个AtomicInteger中, 高3位存储状态, 低29位存储线程数
private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
```

**为什么不推荐其他理解**:
- 避免混淆SHUTDOWN和STOP, SHUTDOWN会处理队列任务, STOP会中断任务
- 理解状态转换是自动的, 不需要手动设置

### 线程池的运行过程

**推荐做法**: 理解线程池执行任务的完整流程

**线程池执行流程**:

1. **提交任务(execute方法)**:
   ```java
   public void execute(Runnable command) {
       if (command == null)
           throw new NullPointerException();
       
       int c = ctl.get();
       // 1. 如果当前线程数 < 核心线程数, 创建新线程
       if (workerCountOf(c) < corePoolSize) {
           if (addWorker(command, true))
               return;
           c = ctl.get();
       }
       
       // 2. 如果线程池运行中且任务成功加入队列
       if (isRunning(c) && workQueue.offer(command)) {
           int recheck = ctl.get();
           // 再次检查, 如果线程池已关闭, 移除任务并拒绝
           if (! isRunning(recheck) && remove(command))
               reject(command);
           // 如果线程数为0, 创建新线程
           else if (workerCountOf(recheck) == 0)
               addWorker(null, false);
       }
       // 3. 如果队列已满, 尝试创建非核心线程
       else if (!addWorker(command, false))
           // 4. 如果创建失败, 执行拒绝策略
           reject(command);
   }
   ```

2. **执行流程图**:
   ```
   提交任务
     ↓
   当前线程数 < 核心线程数?
     ├─ 是 → 创建核心线程执行任务
     └─ 否 ↓
   线程池运行中且队列未满?
     ├─ 是 → 任务加入队列
     │        ↓
     │      队列中有任务且线程数 < 最大线程数?
     │        ├─ 是 → 创建非核心线程处理
     │        └─ 否 → 等待核心线程处理
     └─ 否 ↓
   当前线程数 < 最大线程数?
     ├─ 是 → 创建非核心线程执行任务
     └─ 否 → 执行拒绝策略
   ```

3. **Worker线程执行任务**:
   ```java
   // Worker线程的run方法
   final void runWorker(Worker w) {
       Thread wt = Thread.currentThread();
       Runnable task = w.firstTask;
       w.firstTask = null;
       w.unlock();
       boolean completedAbruptly = true;
       try {
           // 循环获取任务
           while (task != null || (task = getTask()) != null) {
               w.lock();
               // 执行任务
               try {
                   task.run();
               } finally {
                   task = null;
                   w.completedTasks++;
                   w.unlock();
               }
           }
           completedAbruptly = false;
       } finally {
           // Worker退出处理
           processWorkerExit(w, completedAbruptly);
       }
   }
   ```

4. **获取任务(getTask方法)**:
   ```java
   private Runnable getTask() {
       boolean timedOut = false;
       for (;;) {
           int c = ctl.get();
           // 如果线程池已关闭且队列为空, 返回null
           if (runStateAtLeast(c, SHUTDOWN) && (runStateAtLeast(c, STOP) || workQueue.isEmpty())) {
               decrementWorkerCount();
               return null;
           }
           
           int wc = workerCountOf(c);
           // 是否允许核心线程超时 或 当前线程数 > 核心线程数
           boolean timed = allowCoreThreadTimeOut || wc > corePoolSize;
           
           // 从队列中获取任务(带超时)
           Runnable r = timed ?
               workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS) :
               workQueue.take();
           
           if (r != null)
               return r;
           timedOut = true;
       }
   }
   ```

5. **关键参数**:
   - **corePoolSize**: 核心线程数, 即使空闲也保留
   - **maximumPoolSize**: 最大线程数
   - **workQueue**: 任务队列
   - **keepAliveTime**: 非核心线程空闲存活时间
   - **rejectedExecutionHandler**: 拒绝策略

**为什么不推荐其他理解**:
- 避免认为任务会立即执行, 实际可能先加入队列
- 理解核心线程和最大线程的区别, 核心线程不会因为空闲而销毁

### 怎么实现阻塞队列

**推荐做法**: 理解阻塞队列的核心机制, 使用锁和条件变量实现

**阻塞队列实现原理**:

1. **核心机制**
   - 使用ReentrantLock保证线程安全
   - 使用Condition实现阻塞和唤醒
   - 队列满时put操作阻塞, 队列空时take操作阻塞

2. **简单实现示例**:
   ```java
   public class SimpleBlockingQueue<T> {
       private final Object[] items;
       private int putIndex;
       private int takeIndex;
       private int count;
       
       private final ReentrantLock lock;
       private final Condition notEmpty;  // 非空条件
       private final Condition notFull;   // 非满条件
       
       public SimpleBlockingQueue(int capacity) {
           if (capacity <= 0)
               throw new IllegalArgumentException();
           this.items = new Object[capacity];
           this.lock = new ReentrantLock();
           this.notEmpty = lock.newCondition();
           this.notFull = lock.newCondition();
       }
       
       // 入队(阻塞)
       public void put(T item) throws InterruptedException {
           lock.lock();
           try {
               // 队列满时等待
               while (count == items.length) {
                   notFull.await();
               }
               items[putIndex] = item;
               if (++putIndex == items.length)
                   putIndex = 0;
               count++;
               // 唤醒等待取元素的线程
               notEmpty.signal();
           } finally {
               lock.unlock();
           }
       }
       
       // 出队(阻塞)
       public T take() throws InterruptedException {
           lock.lock();
           try {
               // 队列空时等待
               while (count == 0) {
                   notEmpty.await();
               }
               T item = (T) items[takeIndex];
               items[takeIndex] = null;
               if (++takeIndex == items.length)
                   takeIndex = 0;
               count--;
               // 唤醒等待放元素的线程
               notFull.signal();
               return item;
           } finally {
               lock.unlock();
           }
       }
   }
   ```

```java
// execute只能接受Runnable
void execute(Runnable command);

// 使用示例
executorService.execute(() -> {
    System.out.println("执行任务");
    // 异常会被UncaughtExceptionHandler处理
    throw new RuntimeException("异常");
});

// 异常处理方式
Thread.setDefaultUncaughtExceptionHandler((t, e) -> {
    System.out.println("捕获异常: " + e.getMessage());
});
```

3. **ArrayBlockingQueue实现要点**:
   ```java
   // JDK ArrayBlockingQueue的核心实现
   public class ArrayBlockingQueue<E> {
       final Object[] items;
       int takeIndex;
       int putIndex;
       int count;
       
       final ReentrantLock lock;
       private final Condition notEmpty;
       private final Condition notFull;
       
       // put方法
       public void put(E e) throws InterruptedException {
           checkNotNull(e);
           final ReentrantLock lock = this.lock;
           lock.lockInterruptibly();
           try {
               while (count == items.length)
                   notFull.await();
               enqueue(e);
           } finally {
               lock.unlock();
           }
       }
       
       // take方法
       public E take() throws InterruptedException {
           final ReentrantLock lock = this.lock;
           lock.lockInterruptibly();
           try {
               while (count == 0)
                   notEmpty.await();
               return dequeue();
           } finally {
               lock.unlock();
           }
       }
   }
   ```

4. **LinkedBlockingQueue实现要点**:
   - 使用链表结构, 可以无界
   - 使用两把锁(putLock和takeLock)提高并发性能
   - put和take操作可以并发进行

5. **阻塞队列类型**:
   | 队列类型 | 特点 | 适用场景 |
   |---------|------|---------|
   | **ArrayBlockingQueue** | 有界数组, 一把锁 | 固定大小队列 |
   | **LinkedBlockingQueue** | 可选有界链表, 两把锁 | 高并发场景 |
   | **SynchronousQueue** | 不存储元素, 直接传递 | 直接传递场景 |
   | **PriorityBlockingQueue** | 优先级队列 | 需要优先级排序 |
   | **DelayQueue** | 延迟队列 | 定时任务 |

**为什么不推荐其他做法**:
- 避免使用synchronized + wait/notify, ReentrantLock + Condition更灵活
- 避免忘记signal唤醒等待线程, 可能导致死锁
- 理解两把锁的设计可以提升并发性能

### submit 和 execute 有哪些区别

**推荐做法**: 理解submit和execute的区别, 根据需求选择合适的方法

**区别对比**:

| 特性 | execute | submit |
|------|---------|--------|
| **参数类型** | 只接受Runnable | 接受Runnable或Callable |
| **返回值** | void(无返回值) | Future<T>(有返回值) |
| **异常处理** | 异常会被UncaughtExceptionHandler处理 | 异常封装在Future中, 调用get()时抛出 |
| **任务类型** | 只能执行Runnable | 可以执行Runnable或Callable |

**execute方法**:
```java
// submit可以接受Runnable或Callable
<T> Future<T> submit(Callable<T> task);
Future<?> submit(Runnable task);
<T> Future<T> submit(Runnable task, T result);

// 使用示例1: Callable
Future<String> future = executorService.submit(() -> {
    return "结果";
});
String result = future.get(); // 获取结果

// 使用示例2: Runnable
Future<?> future = executorService.submit(() -> {
    System.out.println("执行任务");
    throw new RuntimeException("异常");
});
try {
    future.get(); // 异常会在这里抛出
} catch (ExecutionException e) {
    Throwable cause = e.getCause(); // 获取原始异常
}

// 使用示例3: Runnable with result
Future<String> future = executorService.submit(() -> {
    System.out.println("执行任务");
}, "默认结果");
String result = future.get(); // 返回"默认结果"
```

**底层实现**:
```java
// submit内部会包装Runnable为FutureTask
public <T> Future<T> submit(Callable<T> task) {
    if (task == null) throw new NullPointerException();
    RunnableFuture<T> ftask = newTaskFor(task);
    execute(ftask);  // 最终还是调用execute
    return ftask;
}
```

**推荐使用场景**:
- **execute**: 不需要返回值, 不需要异常处理
- **submit**: 需要返回值, 需要捕获异常, 需要取消任务

**为什么不推荐其他做法**:
- 避免使用execute执行需要返回值的任务, 应使用submit
- 避免忽略submit返回的Future, 应处理异常和结果

### 多线程锁的升级原理是什么

### 说一下 ThreadLocal是什么 有哪些使用场景

### synchronized 的底层实现原理

### synchronized 和 volatile 的区别是什么

### synchronized 和Lock 的区别是什么

### 线程安全的理解

当多个线程访问一个对象时, 如果不用进行额外的同步控制或者其他的协调操作, 调用这个对象的行为获得的结果和在单线程下获得的结果保持一致, 那么就说这个对象是线程安全的

Thread和Runnable实质上是继承关系; 如果有复杂的线程操作需求, 那就选择继承Thread, 如果只是简单的执行一个任务, 那就实现Runnable

### ThreadLocal 的原理和使用场景

使用场景

- 线程间数据隔离
- 进行事务操作, 用于储存线程事务信息
- 数据库连接; session会话管理

Spring 框架在事务开始时会给当前线程绑定一个 Jdbc Connection, 在整个事务过程中都是使用该线程绑定的connection来执行数据库操作, 从而实现了事务的隔离性; Spring框架里面就是用的ThreadLocal来实现这种隔离

ThreadLocal内存泄漏问题

ThreadLocal内存泄漏的根源是由于ThreadLocalMap的生命周期和Thread一样长, 如果没手动删除对应key就会导致内存泄漏问题, 而并不是弱引用的问题

ThreadLocal 正确的使用方法

- 每次使用完ThreadLocal都调用它的remove方法
- 将ThreadLocal变量定义为private static, 这样就一直存在ThreadLocal的强引用, 也就能保证任何时候都能通过ThreadLocal的弱引用访问到Entry的value值, 进而清除掉value的值

### 线程池中阻塞对队列的作用;为什么是先添加队列而不是先创建最大线程

一般的队列只能保证作为一个有限长度的缓冲区, 如果超出了缓冲长度就无法保留当前的任务了, 阻塞队列可以保留住当前想要继续入队的任务

阻塞队列可以保证任务队列中没有任务时阻塞获取任务的线程, 使得线程进入wait状态, 释放cpu资源

阻塞队列自带的阻塞和唤醒功能, 不需要额外处理, 无任务执行时线程池利用阻塞队列的take方法挂起, 从而维持核心线程的存活, 不至于一直占用CPU资源

在创建线程的时候, 是要获取全局锁的, 这个时候其他的线程就得阻塞, 影响了整体效率

### 线程池中线程复用原理

线程池将线程和任务进行解耦; 线程是线程, 任务是任务, 摆脱了之前通过 Thread 创建线程时的一个线程必须对应一个任务的限制

### ConcurrentHashMap原理7和8的区别

jdk1.7

数据结构: ReentrantLock + Segment + HashEntry 一个Segment中包含一个HashEntry数组 每个HashEntry数组中又是一个链表结构

元素查询: 二次hash 第一次hash定位到Segment 第二次hash定位到元素所在的链表的头部

锁: Segment继承至ReentrantLock 锁定操作的Segment 其他的Segment不受影响 并发粒度为segment个数 可以通过构造函数指定 数组扩容不会影响其他的segment

get方法无需加锁 volatile保证

jdk1.8

数据结构: Synchronized + CAS + Node + 红黑树 锁粒度更细 效率更高 

扩容时 阻塞所有的读写操作 并发扩容

写操作无锁 Node的val和next使用volatile修饰 读写线程对该变量可见

数组用volatile修饰 保证扩容时被读线程感知

### synchronized 锁升级的过程

### synchronized在jdk1.8做了哪些优化？

### synchronized为什么要设计成可重入

### mesi协议

### volitale与synchronized的区别

### 如果异步请求需要保障成功, 怎么做

### 有时候多线程, 只需要一个执行, 就是幂等性校验怎么做

## MySQL

### char和varchar的区别

### float和double的区别

- 在内存中占有的字节数不同, 单精度内存占4个字节, 双精度内存占8个字节
- 有效数字位数不同(尾数) 单精度小数点后有效位数7位, 双精度小数点后有效位数16位
- 数值取值范围不同 根据IEEE标准来计算
- 在程序中处理速度不同,一般来说, CPU处理单精度浮点数的速度比处理双精度浮点数快

### mysql的内连接、左连接和右连接有什么区别

### mysql 索引是怎么实现的

### 介绍下MySQL, B+树, 储存引擎

### mysql如何保证ACID

从数据库层面, 数据库通过原子性、隔离性、持久性来保证一致性

binlog + redo log 两阶段提交保证持久性

事务的回滚机制 保证原子性 要么全部提交成功 要么回滚

undo log + MVCC 保证一致性 事务开始和结束的过程不会被其它事务看到 为了并发可以适当破坏一致性

原子性: undo log名为回滚日志, 是实现原子性的关键

持久性: redo log

隔离性: 锁和MVCC机制

### Innodb 是如何实现事务的

Innodb通过 Buffer Pool, Log Buffer, redo log, undo log 来实现事务

- Innodb 在收到一个update语句后, 会根据条件找到数据所在的页, 并将该页缓存在Buffer Pool中
- 执行update语句后, 修改Buffer Pool 中的数据, 也就是内存中的数据
- 针对update语句生成一个redo log对象, 并存入Log Buffer中
- 针对update语句生成undo log日志, 用于事务回滚
- 如果事务提交, 那么则把redo log对象进行持久化, 后续还有其他机制将Buffer Pool中所修改的数据页持久化到磁盘中
- 如果事务回滚, 则利用undo log日志进行回滚

### 强平衡二叉树和弱平衡二叉树有什么区别

强平衡二叉树即AVL树, 而弱平衡二叉树即红黑树

- AVL树比红黑树对于平衡的程度更加的严格, 在相同节点的情况下, AVL树的高度低于红黑树
- AVL树的旋转操作比红黑树的旋转操作更加耗时

### B树和B+树的区别; 为什么MySQL使用B+树

B树的特点

- 节点排序
- 一个节点可以存多个元素, 且多个元素也是有序的

B+树的特点

- 拥有B树的特点
- 叶子节点之间有指针
- 非叶子节点上的元素在叶子节点上都有冗余, 即叶子节点中储存了所有的元素, 并且排好序

MySQL索引使用的是B+树; 在MySQL一个Innodb页就是一个B+树节点, 一个Innodb页默认为16k; B+树可以很好的支持全表扫描和范围查找等SQL语句

### MySQL数据库中什么情况下设置了索引但无法使用

- 没有符合最左前缀原则
- 字段进行了隐式数据类型转化
- 走索引没有全表扫描效率高

### 什么是索引; 索引的优缺点 一般对哪些字段加索引

### binlog 格式

###  MVCC原理说一下

### 最左前缀原则; 为什么要遵守最左前缀原则呢

### 乐观锁、悲观锁, 哪里使用到了

### 哈希索引的使用场景？如何让哈希索引实现范围查找？还能提供优化的方案吗

### explain的字段

### 查询sql语句的执行过程

### 更新sql语句的执行过程

### 什么是crash-safe

### MySQL实现crash-safe的原理

### 两阶段提交的方式会存在什么问题？解决方法

### 假设事务提交过程中, MySQL进程突然奔溃, 重启后是怎么保证数据不丢失的	

### 设一行数据的大小是1k, 在InnoDB中一个3层B+树最多大概可以存放多少行数数据; 主键ID为bigint(8B)类型

### MySQL中哪些情况下会发生死锁

### 如何解决死锁

超时等待、wait-for graph

### 如何避免死锁的发生

### 如何检查是否发生了死锁

死锁日志

### 添加索引时需要注意什么; 什么情况下不适合添加索引

### mysql状态码

### redo log和undo log是如何生成的

### 如何实现可重复读

### 如何解决幻读

### 间隙锁和next-key锁

### mysql锁是锁的什么

索引

### 怎么实现数据库的一致性

### 一个方法里有多个DML 要修改不同数据库集群中的表 其中一条DML执行失败后 怎么进行回滚

分布式事务 是两阶段三阶段

### 了解binlog吗; 知道binlog的底层吗; binlog里面具体记录是什么东西

### Innodb一颗B+数可以存放多少行数据

假设一行数据大小为1k

假设主键id为bigint占用8个字节 指针大小在Innodb 源码中设置为6个字节 一共14个字节

一个页为16kb 一个页可以存放16行数据

只有叶子节点存放数据 非叶子节点只存放索引信息和下一层节点的指针信息(14个字节)

所以非叶子节点可以存放 16 * 1024 = 16384  16384  / 14 = 1170个指针 所以一个节点就是一页 页与页之间指针相连

高度为2的B+树 一个根节点和若干个叶子节点可以存放1170 * 16 = 18720条数据

高度为3即再乘以1170 大约是2千万条

### MySQL中索引命中机制是什么

### MyISAM对比InnoDB 引擎

MyISAM

- 不支持事务，无法保证数据一致性，但是无事务开销，查询更快
- 适合只读或低并发写入场景（如日志存储)
- 表级锁：任何写操作都会锁住整张表，导致并发性能差
- 读操作不会阻塞读，但写操作会阻塞所有读写
- 非聚集索引(索引和数据分离)
- 崩溃后可能数据损坏，需手动修复
- 无事务日志，恢复能力弱

InnoDB

- 支持 ACID 事务
- 适合高并发写入
- 行级锁：仅锁定被修改的行，提高并发性能
- 支持意向锁(IS/IX)优化多粒度锁定
- 聚集索引(主键索引包含数据行)，但是非主键索引(二级索引)需回表查询
- 通过 redo log(重做日志) 和 undo log(回滚日志)保证数据一致性
- 支持崩溃自动恢复(WAL 机制)

如何选择

默认选择 InnoDB(MySQL 5.5+ 默认引擎)，除非有特殊需求

MyISAM 适用场景

- 读多写少：如日志分析、数据仓库、表几乎只读(如报表)
- 不需要事务和行锁：如缓存表、临时计算表
- 存储引擎限制(如旧系统兼容)

## Spring

### Spring 中的 Bean 是线程安全的吗

Spring本身并没有对Bean做线程安全的处理

如果 Bean是无状态的, 那么Bean就是线程安全的

如果 Bean是有状态的, 那么Bean就是线程不安全的

此外Bean是不是线程安全的和Bean的作用域没有关系, Bean的作用域只是表示Bean的生命周期范围, 对于任何生命周期的Bean都是一个对象

### Spring 中使用了哪些设计模式

- 工厂模式: BeanFactory; FactoryBean
- 适配器模式: AdvisorAdapter接口, 对Advisor进行了适配
- 访问者模式: PropertyAccessor接口: 属性访问器, 用来设置和访问某个对象的某个属性
- 装饰器模式: BeanWrapper
- 代理模式: AOP
- 观察者模式: 事件监听机制; Spring 事件驱动模型
- 策略模式: InstantiationStrategy 根据不同的情况进行实例化
- 模板模式: JdbcTemplate
- 委派模式: BeanDefinitionParserDelegate
- 责任链模式: BeanPostProcessor

### Spring中事务传播机制

- REQUIRED: 默认的传播特性; 如果没有事务, 则新建一个事务; 如果当前存在事务 则加入这个事务
- SUPPORTS: 当前存在事务, 则加入当前事务; 如果当前没有事务, 则以非事务的方式进行
- MANDATORY: 当前存在事务, 则加入当前事务; 如果当前事务不存在, 则抛出异常
- REQUIRED_NEW: 创建一个新事务; 如果存在当前事务, 则挂起当前事务
- NOT_SUPPORTED: 以非事务方式执行; 如果存在当前事务, 则挂起当前事务
- NEVER: 不使用事务; 如果当前事务存在, 则抛出异常
- NESTED: 如果当前事务存在, 则在嵌套事务中执行, 否则和REQUIRED的操作一样

### Spring 是什么

### BeanFactory 和 ApplicationContext 有什么区别

ApplicationContext是BeanFactory 的子接口

ApplicationContext 提供了更完整的功能

> 继承了MessageSource 因此支持国际化
>
> 统一的资源文件访问方式
>
> 提供了在监听器中注册bean的事件
>
> 同时加载多个配置文件
>
> 载入多个上下文, 使得每一个上下文都专注于一个特定的层次; 比如应用的web层

- BeanFactory 采用的是延迟加载的形式来注入Bean的, 即只有在使用到某个Bean时(调用getBean()) 才会对该Bean进行实例化; 如果Bean的某一个属性没有注入, BeanFactory 加载后直至第一次使用调用getBean方法才会抛出异常
- ApplicationContext 在容器启动时一次创建了所有的Bean
- ApplicationContext 启动后预载入了所有的单实例Bean, 当使用某个Bean时不用等待
- ApplicationContext 会占用内存空间, 当应用程序配置Bean较多时, 程序启动较慢
- BeanFactory 通常以编程的方式被创建, ApplicationContext还能以声明的方式创建, 如使用 ContextLoader
- BeanFactory 和 ApplicationContext 都支持 BeanPostProcessor 和 BeanFactoryPostProcessor, 但是前者需要手动注册, 而后者则是自动注册

### @Bean 和 @Conponent 的区别

- 作用对象不同:  @Conponent作用于类 而@Bean作用于方法
- 使用方法不同: @Conponent 直接标注在类声明上即可, 而 @Bean 需要在配置类中(@Configuration)使用
- 实现不同: @Conponent 通常是通过类路径扫描(@ComponentScan)来自动装配到Spring容器中, 而 @Bean 通常在配置类中(@Configuration)定义产生Bean
- 灵活性不同: @Bean 比 @Conponent 更灵活, 比如需要注册第三方 Bean是只能通过 @Bean, 因为没有办法在第三方类库中添加 @Component注解

### 如何实现 AOP; 项目中那些地方用到了AOP

利用动态代理技术实现AOP

事务、权限管理、方法的执行时长日志都是通过AOP技术来实现的; 凡是需要对某些方法做统一处理的都可以使用AOP来实现, 利用AOP可以做到业务无入侵

AOP是在Bean生命周期中的初始化后阶段实现的(AbstractAutoProxyCreator)

### Spring 中后置处理器的作用

Spring中的后置处理器分为 BeanFactoryPostProcessor 和 BeanPostProcessor; 它们是Spring底层源码架构设计中非常重要的一种机制, 同时开发者可以使用这两种后置处理器来进行扩展;

BeanFactoryPostProcessor表示针对 BeanFactory 的处理器, Spring处理过程中, 会先创建出BeanFactory实例, 然后利用BeanFactory处理器来加工BeanFactory; 比如Spring的扫描就是基于BeanFactoryPostProcessor来实现的

BeanPostProcessor表示针对Bean的处理器, Spring在创建一个Bean的过程中, 首先会实例化得到一个对象, 然后再利用BeanPostProcessor来对实例对象进行加工; 依赖注入就是基于一个BeanPostProcessor来实现的, 通过该BeanPostProcessor来给实例对象中加了@Autowired注解的属性自动赋值; AOP 也是利用一个BeanPostProcessor来实现的, 基于原实例对象, 判断是否需要进行AOP, 如果需要, 那么就基于原实例对象进行动态代理生成一个代理对象

### 说一下Spring的事务机制

- Spring事务是基于数据库事务和AOP机制的
- 首先对于使用了@Transactional注解的Bean Spring会创建一个代理对象作为Bean
- 当调用代理对象的方法时,会先判断该方法上是否加了@Transactional注解
- 如果加了,那么则利用事务管理器创建一个数据库连接, 并且修改数据库连接的autocommit属性为false, 禁止此连接的自动提交
- 然后执行当前方法, 若方法没有出现异常则直接提交事务
- 若出现了异常, 并且这和异常是需要回滚的就会回滚事务, 否则仍然提交事务
- Spring事务的隔离级别对应的就是数据库的隔离级别
- Spring事务的传播机制是Spring事务自己实现的, 也是Spring事务最复杂的
- Spring事务的传播机制是基于数据库连接来做的, 一个数据连接一个事务; 如果传播机制配置为需要新开一个事务, 那么实际上就是先建立一个数据库连接, 在此新数据库连接上执行sql

### 什么时候@Transactional失效

- 因为Spring事务是基于代理来实现的, 所以某个加了@Transactional的方法只有是被代理对象调用时那么这个注解才会生效; 如果是被代理对象来调用这个方法, 那么@Transactional是不会生效的
- 若某个方法是private的, 那么@Transactional也不会生效, 因为底层cglib是基于继承来实现的, 子类是不能重写父类的private的方法的 所以无法很好的利用代理, 也会导致@Transactional失效
- rollbackFor = RuntimeException.class; 若出现的异常不属于rollbackFor中的异常, 则@Transactional失效

### Spring AOP 什么时候会失效

### BeanFactory和FactoryBean

### Spring怎么管理 Bean 的

### Spring发展历程

### 在Bean初始化前后做扩展, 需要怎么做

### BeanFactoryPostProcessor有了解吗

### Spring做消息监听怎么搞; 用的是哪些接口

### Spring 中的 AOP 底层实现原理

### Spring 中的 IOC 底层实现原理

### Spring 中Bean的注入方式有几种

- 使用 xml 的方式
- 使用@ComponentScan注解来扫描声明了@Controller @Service @Repository @Component注解的类
- 使用@Configuration注解声明配置类 并使用@Bean注解实现Bean的定义 这种方式其实是xml配置方式的以一种演变, 是Spring迈入到无配置化时代的里程牌
- 使用@Import注解 导入配置类或者普通的Bean
- 使用FactoryBean工厂bean 动态构建一个Bean实例 Spring Cloud OpenFeign里面的动态代理实例就是使用FactoryBean来实现的
- 实现ImportBeanDefinitionRegistrar接口 可以动态注入Bean实例 在Spring Boot 里面的启动注解中用到
- 实现ImportSelector接口 动态批量注入配置类或者Bean对象 在Spring Boot里面的自动装配机制里面有用到

### Spring 中@Lazy注解

- 当@Lazy 放在类上 表示在启动的时候不会创建bean对象 当使用的时候才会创建
- 当@Lazy 放在@Bean注解的方法上 表示在启动的时候不会创建bean对象 当使用的时候才会创建
- 当@Lazy 放在@Autowired注解的属性上(也可以是方法或者是方法的参数前面; 构造器或者是构造器参数前面) 并不会直接给属性赋上真正的值 只是会赋值一个代理对象 当真正使用到这个属性的时候 才回去容器中找到一个符合的对象(使用场景 : 循环依赖的时候可以在循环依赖的对象加上@Lazy注解)

### Spring 中两个id相同的会报错吗

如果使用xml配置bean 在同一个配置文件中存在两个相同id的bean 则会报错 但是在两个不同的Spring 文件中可以存在两个id相同的bean Spring 容器会按照bean注册顺序进行覆盖

在Spring 3.x 版本以后 @Configuration + @Bean 取代了xml配置文件 同样只会注册第一个声明Bean的实例 后面id重复的bean则不会再注册了

### Spring 编程式事务中的自动提交具体在哪里关闭的

AbstractPlatformTransactionManager#startTransaction >>> AbstractPlatformTransactionManager#doBegin(不同的事务管理器有不同的实现)

- DataSourceTransactionManager中通过`con.setAutoCommit(false);`

- JpaTransactionManager

  - 通过 EntityManager 控制事务
  - JPA 实现内部已经处理了连接状态

  ```java
  // doBegin方法
  Object transactionData = getJpaDialect().beginTransaction(em,
  					new JpaTransactionDefinition(definition, timeoutToUse, txObject.isNewEntityManagerHolder()));
  // beginTransaction方法
  entityManager.getTransaction().begin();
  ```

- JtaTransactionManager

  - 委托给JTA容器
  - 事务由容器全局管理

  ```java
  // doBegin方法
  doJtaBegin(txObject, definition);
  // doJtaBegin方法
  txObject.getUserTransaction().begin();
  ```

  - ...

在 Spring 的事务管理体系中，`DataSourceTransactionManager` 的 `doBegin()` 方法中显式设置 `setAutoCommit(false)`，而其他事务管理器(如 JPA、JTA 等)没有这个操作，原因为事务控制层级不同

**JDBC 层级事务**(DataSourceTransactionManager)

- 直接操作数据库连接(Connection)
- 需要显式管理自动提交模式
- 必须通过 `setAutoCommit(false)` 开启事务边界

**高级抽象事务** (其他TransactionManager)

- 通过更高层次的API控制事务
- 自动提交由底层资源管理器处理
- 不需要(也不能)直接操作连接对象

背后的设计架构考量

1. **单一职责原则**

   - `DataSourceTransactionManager` 只负责JDBC连接级事务
   - 其他管理器各自处理对应资源的事务语义

2. **抽象层次不同**

   - JDBC是最底层API,需要直接控制连接
   - ORM/JTA等高级API已经封装了事务控制

3. **事务资源差异**

   | 事务管理器类型 | 控制方式            | autoCommit处理位置 |
   | :------------- | :------------------ | :----------------- |
   | DataSource     | 直接操作Connection  | 管理器显式设置     |
   | JPA/Hibernate  | 通过EntityManager   | ORM框架内部处理    |
   | JTA(全局事务)  | 通过UserTransaction | 应用服务器管理     |

### SpringBoot什么时候扫描META-INF/spring.factories文件

setInitializers之后

- SpringApplication构造器中setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
- SpringFactoriesLoader#loadFactoryNames >> loadSpringFactories

refreshContext之前

## 网络

### 301 和 302 代表的是什么 有什么区别

301 Moved Permanently: 永久性重定向

302 Found: 临时性重定向

### 说一下 TCP粘包是怎样产生的

发送方发送的多个数据包, 到接收方缓冲区首尾相连, 粘成一包, 被接收

原因: TCP 协议默认使用 Nagle 算法可能会把多个数据包一次发送到接收方, 应用程读取缓存中的数据包的速度小于接收数据包的速度, 缓存中的多个数据包会被应用程序当成一个包一次读取

解决: 

- 发送方使用 TCP_NODELAY 选项来关闭 Nagle 算法
- 数据包增加开始符和结束, 应用程序读取、区分数据包
- 在数据包的头部定义整个数据包的长度, 应用程序先读取数据包的长度, 然后读取整个长度的包字节数据, 保证读取的是单个包且完整

### get 和 post 请求有哪些区别;

在HTTP1.0中, 定义了三种请求方法GET, POST 和 HEAD方法

在HTTP1.1中, 新增了五种请求方法:OPTIONS, PUT, DELETE, TRACE 和 CONNECT方法 但我们常用的一般就是GET和POST请求

带敏感性的请求POST完全是应该的, 但普通请求(大多数获取数据请求)都应该往GET请求看齐

因为GET请求对于浏览器来说减轻了其压力、而且请求比POST快, 提升页面数据响应、渲染速度

区别具体如下

| 分类                                        | GET                                                          | POST                                                         | 对比                                                         |
| ------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 后退按钮/刷新                               | 无害                                                         | 数据会被重新提交(浏览器应该告知用户数据会被重新提交)         | 每次都重新提交这无疑会对浏览器造成压力, 该问题也是作为 web 端性能优化的重要方向之一 |
| 缓存                                        | 能被缓存                                                     | 不能缓存                                                     | 缓存可以为浏览器减少请求链数, web 端性能优化的重要方向之一   |
| 历史                                        | 参数保留在浏览器历史中                                       | 参数不会保存在浏览器历史中                                   | 相当于缓存, 可减少浏览器压力                                 |
| 对数据长度的限制                            | 当发送数据时, GET 方法向 URL 添加数据；URL 的长度是受限制的(URL 的最大长度是 2048 个字符) | 无限制                                                       | HTTP 协议没有 Body 和 URL 的长度限制, 对 URL 限制的大多是浏览器和服务器的原因；服务器是因为处理长 URL 要消耗比较多的资源, 为了性能和安全(防止恶意构造长 URL 来攻击)考虑, 会给 URL 长度加限制 |
| 安全性                                      | 与 POST 相比, GET 的安全性较差, 因为所发送的数据是 URL 的一部分; 在发送密码或其他敏感信息时绝不要使用 GET | POST 比 GET 更安全, 因为参数不会被保存在浏览器历史或 web 服务器日志中 | 从传输的角度来说, 他们都是不安全的, 因为 HTTP 在网络上是明文传输的, 浏览器F12下什么都一目了然, 或者抓个包, 就能完整地获取数据报文 ；要想安全传输, Encode(转码)当然对于懂的人来说也不安全； 比较安全的只有加密, 也就是 HTTPS |
| 对数据类型的限制                            | 只允许 ASCII 字符                                            | 没有限制; 也允许二进制数据                                   | POST 选择更多                                                |
| url编码( application/x-www-form-urlencoded) | 支持                                                         | 支持多种编码方式                                             |                                                              |

post 请求包含更多的请求头, 因为需要在请求的post部分包含数据, 所以会多了几个数据描述部分的首部字段(如content-type), 这其实是微乎其微的

post在真正接受数据之前会先将请求头发送给服务器进行确认, 然后才真正发送数据

get会将数据缓存起来, 而post不会

post不能进行管道化传输

GET和POST在本质上没有区别, 都是HTTP协议中的两种发送请求的方法 而HTTP呢, 是基于TCP/IP的关于数据如何在万维网中如何通信的协议

HTTP的底层是TCP/IP 所以GET和POST的底层也是TCP/IP, 也就是说, GET/POST都是TCP链接

GET和POST能做的事情是一样一样的 你要给GET加上request body, 给POST带上url参数, 技术上是完全行的通的

有人说POST 比 GET 安全, 因为数据在地址栏上不可见 

然而, 从传输的角度来说, 他们都是不安全的, 因为 HTTP 在网络上是明文传输的, 只要在网络节点上捉包, 就能完整地获取数据报文 

其实, 要想安全传输, 就只有加密, 也就是 HTTPS

Get、Post请求发送的数据包有什么不同吗?

GET请求时产生一个TCP数据包；POST请求时产生两个TCP数据包

- GET:浏览器会把http header和data一并发送出去, 服务器响应200(返回数据)；
- POST:浏览器先发送header, 服务器响应100 continue, 浏览器再发送data, 服务器响应200 OK(返回数据)

Get、POST性能差可以人为忽略

### HTTP协议的header是什么结构

### TCP 和 UDP 的区别

### TCP如何实现拥塞控制的

### TCP 怎么保证可靠传输

### 第三次握手存在的必要性是什么

### 如果TCP断了 client怎么处理

### 单链表和双链表的区别以及应用场景

### HTTP和 HTTPS的区别

HTTPS 是以安全为目标的HTTP通道 简单讲是HTTP的安全版 即HTTP下加入SSL层 HTTPS的安全基础是SSL

HTTPS协议的主要作用

- 建立一个信息安全通道 来保证数据传输的安全
- 确认网站的真实性

区别

- https协议需要到ca申请证书
- http是超文本传输协议 信息是明文传输 https则是具有安全性的ssl加密传输协议
- http和https使用的是完全不同的连接方式 用的端口也不一样
- http的连接简单 是无状态的
- https协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议

### HTTPS的加密过程

### HTTPS 流程

### 公钥和私钥是怎么确立的, 加密用公钥还是秘钥, 解密呢？为什么

### HTTPS加密算法用在哪个步骤

### 滑动窗口

### TCP 如何保证不会接受重复的报文

### TCP 如何保证有序

### 客户端发送的第一个 syn 包丢包会发生什么

### TCP 滑动窗口是干嘛的(流量控制的一种方式), 说一下原理, 概念, 为什么叫滑动窗口

TCP中滑动窗口解决了可靠性以及传输速率问题

TCP协议中, 数据的发送方和接收方都会使用滑动窗口来控制数据的收发 有以下优点

- 避免等待
- 流量控制
- 效率高

滑动窗口的使用避免了数据重复校验, 在字符串匹配等场合有多应用

一个窗口由两个标志位决定

- 左指针指向窗口的左边界
- 右指针指向窗口的右边界

在移动时, 按以下流程

- 固定左边界
- 右边界移动
- 当满足条件时, 移动左边界

###  HTTP状态码有哪些

- 1XX: 信息状态码
- 2xx: 成功状态码
- 3xx: 重定向状态码
- 4xx: 客户端错误状态码
- 5xx: 服务端错误状态码

301 Moved Permanently: 永久性重定向

302 Found: 临时性重定向

303 See Other: 和302有着相同的功能, 但是303明确要求客户端应该采用GET方法获取资源

304 Not Modified: 如果请求报文首部包含一些条件, 例如:If-Match, If-Modified-Since, If-None- Match, If-Range, If-Unmodified-Since, 如果不满足条件, 则服务器会返回 304 状态码

307 Temporary Redirect: 临时重定向, 与302的含义类似, 但是307要求浏览器不会把重定向请求的POST方法改成GET方法

400 Bad Request: 请求报文中存在语法错误

401 Unauthorized: 该状态码表示发送的请求需要有认证信息

403 Forbidden: 请求被拒绝

404 Not Found

500 Internal Server Error: 服务器正在执行请求时发生错误

503 Service Unavailable: 服务器暂时处于超负载或正在进行停机维护, 现在无法处理请求

###  输入一个url用到了哪些协议

### HTTP 访问一个网站 发生了哪些过程

### 三次握手建立起来的连接 在操作系统层面表现的是什么

### HTTP协议由哪几部分组成

### HTTPS是是如何保证安全传输的

### 介绍 TCP HTTP和 HTTPS非对称和对称加密; 为什么对称用的多

### 说下七层网络协议和五层网络协议

OSI 七层模型（Open Systems Interconnection)

- 应用层
- 表示层
- 会话层
- 传输层
- 网络层
- 数据链路层
- 物理层

五层网络协议去掉了 OSI 中冗余的层次(会话层、表示层)，更简洁

互联网(Internet)主要基于 TCP/IP 协议栈

### 丢包发生在哪一层 为什么

### 如果电脑联网失败 是哪一层有问题 为什么

### 怎么发生丢包问题 ping命令 这个命令用到了哪些协议 ICMP协议 这个协议具体是怎么使用的

### 开机时操作系统的整个运转过程是怎样的

### 解释HTTP协议; post和get的区别

### TCP和UDP的区别

### IP协议在哪一层; IP协议的作用

### Ping用的什么协议; 主机是如何区分收到的ICMP包的

### Ping的过程以及原理

### HTTP/1.0和HTTP/1.1的区别是什么; 那你了解HTTP/2么

- 长连接

- 缓存处理

- 带宽优化及网络连接的使用

### 在浏览器中输入URL后发生了什么

### 说一下TCP连接的过程

### HTTP通过什么保证安全传输

### 说一下应用层中使用UDP协议的应用？TCP？UDP+TCP

### TCP和 UDP 分别适用于什么场景

### HTTPS解析过程

### 用户如何找到DNS服务器

### DNS服务器是怎么知道IP地址的; 如何知道去哪里找DNS服务器

### 一个服务可以在443端口同时监听TCP和UDP吗

可以 端口的唯一性的标识不是端口号 而是端口号和协议名称的组合

### HTTPS 会把报文头加密吗

### HTTP1.0和HTTP2.0的区别

- HTTP/2采用二进制格式而非文本格式
- HTTP/2是完全多路复用的 而非有序并阻塞的 只需一个连接即可实现并行
- 使用报头压缩 HTTP/2降低了开销
- HTTP/2让服务器可以将响应主动“推送”到客户端缓存中

## Linux

### 查找一个日志中含有"123"的有多少个

### 查看特定时间的日志

### Linux 杀掉进程的处理流程

### 常用的linux命令有哪些

 awk tr sort uniq命令

### 一段字符串 统计所有单词出现的次数

## JVM

### CMS和G1详细介绍一下

### 讲一讲CMS的回收过程

### 什么是字节码以及它的组成

Java 字节码 是 Java 虚拟机执行的一种指令格式

.class 文件是由十六进制值组成的, JVM以两个十六进制值为一组, 就是以字节为单位进行读取

- 魔数

- 版本号

- 常量池

  >常量池有两类常量: 字面量和符号引用
  >
  >常量池整体分为两个部分: 常量池计数器和常量池数据区

- 访问标志

- 当前类索引

- 父类索引

- 接口索引

- 字段表

- 方法表

- 附件属性

### 使用堆外内存的原因

- 对垃圾回收停顿的改善, 由于堆外内存是直接受操作系统管理而不是JVM, 所以使用堆外内存时可以保持较小的堆内内存规模, 从而在GC时减少回收停顿对应用的影响
- 提升程序I/O操作的性能, 通常在I/O通信过程中, 会存在堆内内存到堆外内存的数据拷贝动作, 对于需要频繁进行内存间数据拷贝且生命周期较短的暂存数据, 都建议储存到堆外内存

JDK8中使用元空间代替永久代的原因: 因为方法区所储存的类信息通常是比较难确定的, 所以对于方法区的大小是比较难指定的, 太小了容易出现方法区溢出, 太大了又会占用太多的虚拟机的内存空间, 干脆将方法区使用堆外内存实现

### 双亲委派模型的优势

### 内存泄露怎么排查

### jvm类加载过程

### 对象头的结构

### 什么情况下会触发 full GC

### JVM 为什么要指令重排

### 什么是TLAB？作用

### 垃圾回收主要回收哪些区域

### 对象什么情况会进去老年代

### 如何判断一个对象是否可回收

### 可达性分析后发现一个对象不在引用链上, 那它一定会被回收吗

### G1回收器采用了什么样的回收算法

### JVM 为什么要指令重排

### 什么区域 什么情况下会发生 OOM

### 什么时候会触发Full GC

### 如何打破双亲委派机制

### 符号引用是什么; 直接引用是什么

### 新生代一般用什么垃圾回收算法;为什么不用标记整理

### 详细说一下标记-清除, 复制和标记-整理算法

### 常用的JVM启动参数有哪些

```properties
# 设置堆内存
-Xmx4g -Xms4g
# 指定GC算法
-XX:+UseG1Gc -XX:MaxGCPauseMillis=50
# 指定GC并行线程数
-XX:ParallelGCThreads=4
# 打印GC日志
-XX:+PrintGCDetails -XX:+PrintGCDateStamps
# 指定GC日志文件
-Xloggc:gc.log
# 指定Meta区的最大值
-XX:MaxMetaspaceSize=2g
# 设置单个线程栈的大小
-Xss1m
# 指定堆内存溢出时自动进行dump(后缀hprof文件)
# hprof 文件是 JVM 生成的一种二进制文件, 用于收集 Java 应用程序的运行时信息
# 分析hprof文件 1 MAT 2 VisualVM 3 JProfiler
-XX+HeapDumpOnOutOfMemoryError
-XX:ErrorFile=/logs/oom_dump/xxx.log
-XX:HeapDumpPath=/logs/oom_dump/xxx.hprof
# 当发生 OOM 时, JVM 会立马退出
-XX:+ExitOnOutOfMemoryError
```

### java8默认使用的垃圾回收器是什么

并行垃圾收集器(Parallel GC)

### 安全点

用户线程暂停 GC线程要开始工作 但是要确保用户线程暂停的这行字节码指令是不会导致引用关系的变化 所以JVM会在字节码指令中 选一些指令作为安全点 比如方法的调用 循环跳转 异常跳转等

主动式中断是设置一个标志 这个标志是中断标志 各业务线程在运行过程中会不停地主动去轮询这个标志 一旦发现中断标志为True 就会在自己最近的安全点上主动中断挂起

安全区域

如果业务线程不在执行状态(Sleep或者Blocked) 那么程序就无法进入安全点 对于这种情况就必须进入安全区域

安全区域可以看作被扩展拉伸了的安全点

当用户线程执行到安全区域里面的代码时 首先会标记自己已经进入到安全区域 这段时间里JVM要发起GC则不必去管这个线程 当线程要离开安全区域时 要判断JVM是否完成了垃圾回收

- 如果完成了 那线程则继续执行
- 否则一直等待 直到收到可以离开安全区域的信号为止

### CMS G1垃圾回收器中的三色标记

三色标记是一种垃圾回收法 它可以让JVM不发生或者仅短时间发生STW 从而达到清除JVM内存垃圾的目的

- 黑色 该对象已经被标记过了且该对象下的属性也全部都被标记过了
- 白色 对象已经被垃圾收集器扫描过了 但是对象中还存在没有扫描的引用
- 灰色 表示对象没有被垃圾回收器访问过 即表示不可达

三色标记的漏标问题

CMS的解决方案 Incremental Update(重新扫描)

G1的解决方案 SATB(snapshot-at-the-beginning)

## 框架

### Spring 自动装配 bean 有哪些方式

### Spring 实现事务方式有哪些

### 说一些 Spring MVC 的运行流程

### Spring MVC 有哪些组件

### Spring Boot 和 Spring 的区别

### Spring Boot 的启动流程

### ES 写入数据和查询数据的工作原理是什么

### 底层的 Lucene 介绍一下

### 倒排索引的原理说一下 ES 在数据量很大的情况下, 比如数十亿级别, 如何提高查询效率啊

### ES 生产集群的部署架构是什么

### Nacos集群CP架构底层类Raft协议怎么实现的

### Sentinel底层是如何计算线上系统实时QPS的

### 不使用redis 存储session该怎么做

### 缓存的雪崩里面加上的随机值如何选取

### 讲一下负载均衡

### 注解底层怎么实现的

### 注解失效有哪些原因

### cookie 和 session; session存储在服务端哪里

- 存储位置不同 cookie的数据信息存放在客户端浏览器上; session的数据信息存放在服务器上
- 存储容量不同 单个cookie保存的数据<=4KB, 一个站点最多保存20个Cookie; 对于session来说并没有上限, 但出于对服务器端的性能考虑, session内不要存放过多的东西, 并且设置session删除机制
- 存储方式不同 cookie中只能保管ASCII字符串, 并需要通过编码方式存储为Unicode字符或者二进制数据; session中能够存储任何类型的数据
- 隐私策略不同 cookie对客户端是可见的, 别有用心的人可以分析存放在本地的cookie并进行cookie欺骗, 所以它是不安全的; session存储在服务器上, 对客户端是透明对, 不存在敏感信息泄漏的风险
- 有效期上不同 开发可以通过设置cookie的属性, 达到使cookie长期有效的效果; session依赖于名为JSESSIONID的cookie, 而cookie JSESSIONID的过期时间默认为-1, 只需关闭窗口该session就会失效, 因而session不能达到长期有效的效果
- 服务器压力不同 cookie保管在客户端, 不占用服务器资源; session是保管在服务器端的, 每个用户都会产生一个session
- 浏览器支持不同 cookie是需要客户端浏览器支持的, 假如客户端禁用了cookie, 或者不支持cookie, 则会话跟踪会失效, 关于WAP上的应用, 常规的cookie就派不上用场了; 运用session需要使用URL地址重写的方式, 一切用到session程序的URL都要进行URL地址重写, 否则session会话跟踪还会失效
- 跨域支持上不同 cookie支持跨域名访问; session不支持跨域名访问

### 说一下NGINX的反向代理和正向代理

### nginx(反向代理、负载均衡)

### rpc了解过没有

### 处理2.5亿的数据, 怎么样找出不重复的数据

(使用2bitmap, 00表示不存在, 01表示唯一, 10表示重复)

## 设计模式

### 设计模式有哪些大类

创建型

- 工厂模式(Factory Pattern)
- 抽象工厂模式(Abstract Factory Pattern)
- 单例模式(Singleton Pattern)
- 建造者模式(Builder Pattern)
- 原型模式(Prototype Pattern)

结构型

- 适配器模式(Adapter Pattern)
- 桥接模式(Bridge Pattern)
- 过滤器模式(Filter、Criteria Pattern)
- 组合模式(Composite Pattern)
- 装饰器模式(Decorator Pattern)
- 外观模式(Facade Pattern)
- 享元模式(Flyweight Pattern)
- 代理模式(Proxy Pattern)

行为型

- 责任链模式(Chain of Responsibility Pattern)
- 命令模式(Command Pattern)
- 解释器模式(Interpreter Pattern)
- 迭代器模式(Iterator Pattern)
- 中介者模式(Mediator Pattern)
- 备忘录模式(Memento Pattern)
- 观察者模式(Observer Pattern)
- 状态模式(State Pattern)
- 空对象模式(Null Object Pattern)
- 策略模式(Strategy Pattern)
- 模板模式(Template Pattern)
- 访问者模式(Visitor Pattern)

### 设计模式的六大原则

开闭原则

里氏替换原则

依赖倒转原则

接口隔离原则

迪米特法则

合成复用原则

### 简单讲讲策略模式, 观察者模式有了解吗

### 工厂模式, 简单工厂和抽象工厂的区别

### 简单工厂存在的问题

## 算法

两个栈实现队列

打印二叉树的左边界

二叉树转双向链表

k个反转链表

最长重复子串

接雨水

(滑动窗口)求数组中连续k个数的最大平均值 

二叉树的序列化和反序列化

加油站问题

链表元素的交换

lc(227)计算器

[leetcode](https://www.nowcoder.com/jump/super-jump/word?word=leetcode)[451. 根据字符出现频率排序](https://leetcode-cn.com/problems/sort-characters-by-frequency/)

求一颗二叉树的平均高度

旋转数组二分

连续子数组最大和

两个链表相加

k个一组反转链表

判断A是否是B的子树

最长回文子串

链表重新排序

最大无重复子串长度

螺旋二维数组

下一个排列

括号匹配

LRU缓存数据结构

有序数组生成平衡二叉树

二叉树中的最大路径和

顺时针打印矩阵

二叉树的公共祖先, 要求递归直接返回结果, 不使用全局变量
