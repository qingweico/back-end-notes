# redis

[TOC]

## redis基本知识

redis: 一种分布式的内存数据库缓存

NoSQL: Not Only SQL "不仅仅是SQL" 泛指非关系型数据库

单进程

redis默认16个库,下标从0开始,初始化使用0号库

大数据时代的三V: Volume(海量) Variety(多样) Velocity(实时)

互联网三高: 高性能 高可用 高扩展 

高性能: 响应(低延迟) 吞吐(高吞吐量 高并发)

TPS QPS

amdahl定律

### RDBMS和NoSQL的区别

```properties
RDBMS: 高度组织化结构化的数据
      结构化查询语言(SQL)
      数据操纵语言,数据定义语言
      严格的一致性
      基础事务
NoSQL: 代表的不仅仅是SQL
      没有声明性查询语言
      没有预定义的模式
      键值存储 文档存储 列存储 图形存储
      最终一致性而非ACID属性
      非结构化和不可预知的数据
      CAP属性
      高性能 高可用 可伸缩性  
```

### 分布式

不同的多台服务器上面部署不同的服务模块(工程),他们之间通过RPC(Remote Procedure Call  远程过程调用)/RMI之间通信和调用,对外提供服和组内协作

### 集群

不同的多台服务器上面部署相同的服务模块,通过分布式调度软件进行统一的调度,对外提供访问和服务

### 分布式数据库中的CAP和BASE

#### CAP

- C: Consistency(强一致性)
- A: Availability(可用性)
- P: Partition tolerance(分区容错性)这里的分区不是指磁盘分区 而是指网络分区

#### BASE

- 基本可用(Basically Available)读写不报错
- 软状态(Soft State)只模糊的知道大体的状态 不知道准确的状态
- 最终一致(Eventually Consistent)经过一段时间后 系统状态最终保持一致

Redis 不是一个强一致性的系统 主打最终一致性

### Key

```bash
set k1 v1
set k2 v2       # 设置键值
get k1
get k2          # 获取键值
set k1 value1   # 会覆盖当前的值
```

```bash
move k1 2;      # 将键k1的值移动到2号库
```

```bash
exists k1       # 判断某个键是否存在
```

```bash
expire k1 10    # 设定键k1过期时间(s)
```

```bash
ttl k1          # 查看键k1剩余过期时间  -1代表永不过期 -2代表已过期 过期即从当前库中移出该键值对
```

```bash
type k          # 查看k的数据类型
```

`dbsize` 查看当前数据库的key数量

`flushdb` 清空当前库

`flushall` 清空所有的库

`keys *` 查看当前库的所有的键

`keys k?` 查看当前库中以`k` 开头的键

`select index(0~15)` 选择库

## Redis5种数据类型

### String(字符串)(一key单value)

string是redis最基本的类型,一个key对应一个value string是二进制安全的,redis的string可以包含任何数据,一个redis的string的value最多可以是512M

string类型的内部编码有三种

- int 储存八个字节的长整型
- embstr 代表embstr格式的SDS(Simple Dynamic String) 简单动态字符串, 用来储存小于44个字节的字符串, 只分配一次内存空间(因为Redis和SDS是连续的)
- raw:  储存大于44个字节的字符串, 需要分配两次内存空间

redis中浮点数和大整数怎么存

```bash
del k1           # 删除k1
```

```bash
append k1 value  # 向k1后面追加value
```

```bash
strlen k1        # 查看k1的长度
```

-----

k的值一定要为数字类型

```bash
incr k1          # 单步增加k1的值
```

```bash
decr k1          # 单步递减k1的值
```

```bash
incrby k1 2      # 自定义步长增加k1的值
```

```bash
decrby k1 2      # 自定义步长递减k1的值
```

-----

```bash
set k abcdefg;
GETRANGE k 0 3;  # "abcd"  不会改变原来的值
```

```bash
SETRANGE k 0 ABCD;
get k;            # "ABCDefg" 从索引0开始重新设置 会覆盖原来的值
```

-----

```bash
setex k 10 v;
=>
set k v;
expire k 10;
```

```bash
setnx k 10 v;             # 设置不存在的键的过期时间,若存在则设置失败
```

-----

```bash
mset k1 v1 k2 v2 k3 v3    # 一次性设置多个键值对
```

```bash
mget k1 k2 k3             # 一次性获取多个值
```

```bash
msetnx k1 v1 k5 v5        # 若存在已有的键,则设置失败,一个都不会生效
```

### set(集合) (无序且唯一  一key多value)

实现:  字典

```bash
smembers set              # 查看集合中所有的元素
sadd set 1 2 1 2 3 3      # 会去重,保证元素的唯一性
smember set
    =>
1) "1"
2) "2"
3) "3"
sismember set 9      # 判断set是否存在该元素
scard set            # 获取set中元素的个数
SREM set 1           # 删除set中值为"1"的元素   
SRANDMEMBER set 5    # 随机出现5个元素
spop set             # 随机出栈一个元素
```

```bash
smove set1 set2 value //将set1中的value移动到set2中
```

```bash
sdiff set set1  # 差集
sinter set set1 # 交集
sunion set set1 # 并集
```

### hash(哈希)(一key多value)

Hash结构可以将对象中每个字段独立储存 可以针对单个字段做crud 并且内存占用更少

实现:  哈希表

```bash
hset student name "jack"        # student为键  name 和 "jack" 键值对为值
hget student name               # 获取student中name的值
hmset student name "jack age 21"# 一次性插入多个值
hmget student name age          # 一次性获取多个值
hgetall student                 # 获取键中所有的值
HDEL student age                # 删除单个值
hlen student                    # 获取student的value的长度
HEXISTS student age             # 判断student是否存在某一个值
HKEYS student                   # 查询student中所有的键
HVALS student                   # 查询student中所有的键对应的值
HINCRBY student age 2           # 自定义步长增加age的值
HINCRBYFLOAT student age 3.0    # 自定义步长以浮点类型增加
HSETNX student number 1         # 设置student中不存在的键值 若存在则失败
```

### list (列表) (字符串链表) (一key多value)

结构:  LinkedList

lrange

lindex

```bash
Lpush list 1 2 3 4 5 # 相当于从左边入栈
Lrange list 0 -1 
=>
1) "5"
2) "4"
3) "3"
4) "2"
5) "1"
Lpop list #  "5"
Rpop list #  "1"    
```

```bash
Rpush list 1 2 3 4 5  # 相当于从右边入栈
Lrange list 0 -1
=>
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
Lpop list       # "1"
Rpop list       # "5"
Lindex list 2   # "4" 按照索引从上往下获取元素
llen list //3 长度
```

```bash
LREM list 3 4   # 删除list中3个4
```

```bash
lpush list 1 2 3 4 5 6 7 8
LTRIM list 0 5  # 将索引从0到5(包括5)的元素截取并重新赋值给list
LRANGE list 0 -1   
   = >
    1) "8"
    2) "7"
    3) "6"
    4) "5"
    5) "4"
    6) "3
```

```bash
rpush list1 4 5 6 7
rpush list2 1 2 3
rpoplpush list1 list2 # "7" 将list1的栈底元素弹出压入到list2栈顶上
lrange list2 0 -1
    = >
1) "7"
2) "1"
3) "2"
4) "3"
LSET list2 0 9        # 将索引为0的元素替换为9
1) "9"
2) "1"
3) "2"
4) "3"
LINSERT list2  before/after 9 10  # 在元素"9"的前面(后面)插入元素"10"

```

### Zset(有序集合)(sorted set)

```bash
zadd zset 1 v1 2 v2 3 v3
zrange zset 0 -1  # 正向获取元素
    =>
1) "v1"
2) "v2"
3) "v3"
ZREVRANGE zset 0 -1 # 逆序获取元素
    =>
1) "v2"
2) "v1"
zrange zset 0 -1 withscores # 正向获取元素带有score
    =>
1) "v1"
2) "1"
3) "v2"
4) "2"
5) "v3"
6) "3"
ZRANGEBYSCORE zset  2 3 # 获取元素按指定的score范围
    =>
1) "v2"
2) "v3"
ZRANGEBYSCORE zset  2 (3    # "(" 不包括
    =>
1) "v2"
ZRANGEBYSCORE zset  2 3 limit 0 1 # 从结果集的下标0开始,选择一个元素
    =>
1) "v2"
ZREM zset v3                      # 删除元素
    =>
1) "v1"
2) "v2"
ZCARD zset       # 统计zset中元素的个数
    => 
(integer) 2
ZCOUNT zset 1 2  # 统计指定范围内的元素的个数
    =>
(integer) 2
ZRANK zset v2    # 拿到指定值的下标
    =>
(integer) 1
ZSCORE zset v1   # 获取v1对应的score
    =>
"1"
ZREVRANK zset v2 # 逆序获得元素的下标
"0"
ZREVRANGEBYSCORE zset 2 1 # 逆序获取元素按照指定的score范围    
```

