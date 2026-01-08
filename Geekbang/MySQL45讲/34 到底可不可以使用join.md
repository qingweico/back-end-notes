[TOC]

## Index Nested-Loop Join

```sql
CREATE TABLE `t2` (
  `id` int(11) NOT NULL,
  `a` int(11) DEFAULT NULL,
  `b` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `a` (`a`)
) ENGINE=InnoDB;

create table t1 like t2;
insert into t1 (select * from t2 where id<=100)

select * from t1 straight_join t2 on (t1.a=t2.a);
```

- 如果直接使用 join 语句,MySQL 优化器可能会选择表 t1 或 t2 作为驱动表,而使用 straight_join 让 MySQL 使用固定的连接方式执行查询,这样优化器只会按照我们指定的方式去 join
- 对驱动表 t1 做了全表扫描 
- 对于 扫描 t1 的每一行 R,根据 a 字段去表 t2 查找,走的是树搜索过程
- 驱动表是走全表扫描,而被驱动表是走树搜索(针对上面的sql 不是所有情况)

### 时间复杂度

- 假设被驱动表的行数是 M;每次在被驱动表查一行数据,要先搜索索引 a,再搜索主键索引;每次搜索一棵树近似复杂度是以 2 为底的 M 的对数,记为 log2M,所以在被驱动表上查一行的时间复杂度是 2`*`log2M (二级索引 + 回表 都是 log2M)
- 假设驱动表的行数是 N,执行过程就要扫描驱动表 N 行,然后对于每一行,到被驱动表上匹配一次

- 因此整个执行过程,近似复杂度是 N + N`*`2`*`log2M
- 显然,N 对扫描行数的影响更大,因此应该让小表来做驱动表(这个结论的前提是"可以使用被驱动表的索引")

## Simple Nested-Loop Join

```sql
select * from t1 straight_join t2 on (t1.a=t2.b);
```

- 由于表 t2 的字段 b 上没有索引,每次到 t2 去匹配的时候,就要做一次全表扫描
- t1 全表扫描 乘以 t2 全表扫描, 总共扫描 M x N 行
- 但是MySQL 没有使用这个 Simple Nested-Loop Join 算法,而是使用了另一个叫作"Block Nested-Loop Join"的算法,简称 BNL

## Block Nested-Loop Join

当被驱动表上没有可用的索引时

- 把表 t1 的数据读入线程内存 join_buffer 中,由于这个语句中写的是 select *,因此是把整个表 t1 放入了内存
- 扫描表 t2,把表 t2 中的每一行取出来,跟 join_buffer 中的数据做对比,满足 join 条件的,作为结果集的一部分返回
- 扫描 M + N 行数,在内存中做判断,仍然是 M x N次
- join_buffer 的大小是由参数 join_buffer_size 设定的,默认值是 256k;如果放不下表 t1 的所有数据话,策略很简单,就是分段放
- join_buffer_size 越大,一次可以放入的行越多,分成的段数也就越少,对被驱动表的全表扫描次数就越少,建议如果你的 join 语句很慢,就把 join_buffer_size 改大
- 但是如果 join_buffer 以及 M 和 N 大小确定的情况,驱动表分几次放入join_buffer 中,会导致被驱动表被扫描几次,所以驱动表行数小,整体扫描的次数会小,所以应该让小表当驱动表

## 能不能使用 join 语句

- 如果可以使用 Index Nested-Loop Join 算法,也就是说可以用上被驱动表上的索引,其实是没问题的
- 如果使用 Block Nested-Loop Join 算法,扫描行数就会过多;尤其是在大表上的 join 操作,这样可能要扫描被驱动表很多次,会占用大量的系统资源;所以这种 join 尽量不要用
- 可以通过 explain 结果里面 Extra 字段里有没有出现"Block Nested Loop"字样

## 驱动表选择

- 如果是 Index Nested-Loop Join 算法,应该选择小表做驱动表
- 如果是 Block Nested-Loop Join 算法
  - 在 join_buffer_size 足够大的时候,是一样的
  - 在 join_buffer_size 不够大的时候(这种情况更常见),应该选择小表做驱动表
- 所以总是应该使用小表做驱动表

## 什么叫做小表

两个表按照各自的条件过滤,过滤完成之后,计算参与 join 的各个字段的总数据量,数据量小的那个表,就是"小表",应该作为驱动表

## 补充

Hash Join 是 MySQL 8.0 的重要优化,对于大表 JOIN 且无索引的场景,性能提升非常显著, 其在explain 中 Extra 字段中显示为 Using join buffer (hash join)

基本实现过程

- 扫描驱动表
- 为 JOIN 字段计算哈希值,建立哈希表(存储在内存中,太大会溢出到磁盘)
- 扫描被驱动表,对每行的 JOIN 字段计算哈希值
- 在哈希表中查找匹配项,找到则放到结果集中

