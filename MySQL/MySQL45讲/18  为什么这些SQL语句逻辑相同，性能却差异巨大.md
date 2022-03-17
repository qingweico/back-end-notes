```mysql
# 交易记录表 tradelog 包含交易流水号（tradeid）、交易员 id（operator）、交易时间（t_modified）等字段
CREATE TABLE `tradelog` (
  `id` int(11) NOT NULL,
  `tradeid` varchar(32) DEFAULT NULL,
  `operator` int(11) DEFAULT NULL,
  `t_modified` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `tradeid` (`tradeid`),
  KEY `t_modified` (`t_modified`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

### 条件字段函数操作

```mysql
 select count(*) from tradelog where month(t_modified)=7;
```

如果对字段做了函数计算，就用不上索引了，这是 MySQL 的规定

条件是 where t_modified='2018-7-1’的时候可以用上索引，而改成 where month(t_modified)=7 的时候就不行

对索引字段做函数操作，可能会破坏索引值的有序性，因此优化器就决定放弃走树搜索功能,但是优化器并不是要放弃使用这个索引，放弃了树搜索功能，优化器可以选择遍历主键索引，也可以选择遍历索引 t_modified

所以说，当用了explain语句分析的时候，并不是看到用了索引，就觉的很快了，有些时候，优化器是觉的遍历主键索引的成本要比遍历普通索引要高，所以，通过扫描行数可以看出还是遍历

由于在 t_modified 字段加了 month() 函数操作，导致了全索引扫描。为了能够用上索引的快速定位能力，我们就要把 SQL 语句改成基于字段本身的范围查询

即使是对于不改变有序性的函数，也不会考虑使用索引。比如，对于 select * from tradelog where id + 1 = 10000 这个 SQL 语句，这个加 1 操作并不会改变有序性，但是 MySQL 优化器还是不能用 id 索引快速定位到 9999 这一行。所以，需要你在写 SQL 语句的时候，手动改写成 where id = 10000 -1 才可以

### 隐式类型转换

```mysql
select * from tradelog where tradeid=110717;
```

交易编号 tradeid 这个字段上，本来就有索引，但是 explain 的结果却显示，这条语句需要走全表扫描。你可能也发现了，tradeid 的字段类型是 varchar(32)，而输入的参数却是整型，所以需要做类型转换

```mysql
mysql> select "10" > 9;
+----------+
| "10" > 9 |
+----------+
|        1 |
+----------+
1 row in set (0.01 sec)
```

在 MySQL 中，字符串和数字做比较的话，是将字符串转换成数字

相当于

```mysql
# 对索引字段做函数操作, 优化器会放弃走树搜索功能
select * from tradelog where  CAST(tradid AS signed int) = 110717;
```

id 的类型是 int，如果执行下面这个语句，是否会导致全表扫描呢

```mysql
select * from tradelog where id="83126";
```

不会 因为字符串在右边 并不是索引字段上 所以不会在索引字段上使用函数(将字符串转换为整数)

### 隐式字符编码转换

```mysql
select d.* from tradelog l, trade_detail d where d.tradeid=l.tradeid and l.id=2; /*语句Q1*/
```

两个表的字符集不同，一个是 utf8，一个是 utf8mb4，所以做表连接查询的时候用不上关联字段的索引

CONVERT() 函数，在这里的意思是把输入的字符串转成 utf8mb4 字符集

对索引字段做函数操作，优化器会放弃走树搜索功能

连接过程中要求在被驱动表的索引字段上加函数操作，是直接导致对被驱动表做全表扫描的原因

如果CONVERT 函数是加在输入参数上的，这样就可以用上被驱动表的索引

```mysql
# 修改字段上字符集
alter table trade_detail modify tradeid varchar(32) CHARACTER SET utf8mb4 default null;
```

如果数据量比较大， 或者业务上暂时不能做这个 DDL 的话，那就只能采用修改 SQL 语句的方法了

```mysql
select d.* from tradelog l , trade_detail d where d.tradeid=CONVERT(l.tradeid USING utf8) and l.id=2; 
```

