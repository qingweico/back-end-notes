[TOC]

演示的表结构和数据

```mysql
CREATE TABLE `t` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `c` int(11) DEFAULT NULL,
  `d` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `c` (`c`)
) ENGINE=InnoDB;

insert into t values(null, 1,1);
insert into t values(null, 2,2);
insert into t values(null, 3,3);
insert into t values(null, 4,4);

create table t2 like t
```

## insert … select 语句加锁

开始

```mysql
-- 事务一执行
begin;
insert into t2(c,d) select c,d from t;

-- 事务二(会被阻塞)
INSERT INTO t
VALUES
	(- 1, - 1, - 1 );
```

原因就是事务一的语句会对表 t 的所有行和间隙加锁,目的就是保证日志和数据的一致性

主要的事务超时参数通过`innodb_lock_wait_timeout`控制,默认50 秒

为什么会出现不一致

- 如果没有锁,在 binlog_format=statement 的情况下,binlog会出现如下的记录

```mysql
insert into t values(-1,-1,-1);
insert into t2(c,d) select c,d from t;
```

- 这个语句到了备库执行,就会把 id=-1 这一行也写到表 t2 中,出现主备不一致

## insert 循环写入

执行 insert … select 的时候,对目标表也不是锁全表,而是只锁住需要访问的资源

目标执行语句

```mysql
insert into t2(c,d)  (select c+1, d from t force index(c) order by c desc limit 1);
```

整条语句的扫描行数是 1, 但是插入到本表中呢,目标语句

```mysql
insert into t(c,d)  (select c+1, d from t force index(c) order by c desc limit 1);
```

- 通过explain的Extra字段可以看到,会使用到临时表(内部临时表)
- 默认临时表是使用 Memory 引擎的,全表扫描t,读取后插入到临时表中
- 语句中有limit 1,所以只取了临时表的一行,插入到t中

问题: 对表t做全表扫描,并且会给索引 c 上的所有间隙都加上共享的 next-key lock,所以这个语句执行期间,其他事务不能在这个表上插入数据

注: 通过 `	SHOW STATUS LIKE '%Innodb_rows_read%'` 可以查看InnoDB 扫描了多少行

为什么需要临时表

- 原因是这类一边遍历数据,一边更新数据的情况,如果读出来的数据直接写回原表,就可能在遍历过程中,读到刚刚插入的记录,新插入的记录如果参与计算逻辑,就跟语义不符

优化思路就是:先将数据插入到内存临时表中(扫描一行),在从临时表中取出这行数据插入

```mysql
create temporary table temp_t(c int,d int) engine=memory;
insert into temp_t  (select c+1, d from t force index(c) order by c desc limit 1);
insert into t select * from temp_t;
drop table temp_t;
```

## insert 唯一键冲突

在可重复读(repeatable read)隔离级别下

当执行insert 语句,发生唯一键冲突的时候,并不只是简单地报错返回,还在冲突的索引上加了锁,无论是主键索引还是唯一索引,这两类索引冲突加的都是 next-key lock

### 回忆下 next-key lock

```mysql
next-key lock = Gap Lock + Record Lock
              = (prev_key, current_key]
```

因为锁是和某条索引记录相关的,所以先找 redocd ,再在这个record 负责的区间上加上 next-key lock

注:一个 next-key lock 就是由它右边界的值定义的,比如下面的语句

```mysql
-- 在 REPEATABLE READ 下,实际上会加 Next-Key Lock, 锁定 id=30 以及它前面的间隙
SELECT * FROM t WHERE id = 30 FOR UPDATE;
```

但是上面实际上是只加 record lock,是因为锁优化,因为 id 是主键,又是等值查询,所以这里的 gap就是多余的,即当前记录之前的 gap"不可能产生幻读,就被移除掉,所以next-key lock就退化成了record lock

- 逻辑上、对外表现为:只加 Record Lock(行锁)
- 实现上:InnoDB 仍然是 next-key lock 机制,但退化为 record lock

### 死锁场景

| 时间   | Session A                                    | Session B                           | Session C                                                    | 锁状态分析                                                   |
| :----- | :------------------------------------------- | :---------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **T1** | `BEGIN;` `INSERT INTO t VALUES(NULL, 5, 5);` | 空闲                                | 空闲                                                         | A 获取了:  a=5 的 Record Lock(如果存在)                   |
| **T2** | 持有锁                                       | `INSERT INTO t VALUES(NULL, 5, 5);` | `INSERT INTO t VALUES(NULL, 5, 5);`                          | session B 要执行相同的 insert 语句,发现了唯一键冲突,加上读锁;session C 也在索引 c 上,c=5 这一个记录上,加了读锁 |
| **T3** | `ROLLBACK;`                                  | 成功插入                            | 死锁错误(1213 - Deadlock found when trying to get lock; try restarting transaction) | session A 回滚; 这时候,session B 和 session C 都试图继续执行插入操作,都要加上写锁; 两个 session 都要等待对方的行锁,所以就出现了死锁 |

现象:在 session A 执行 rollback 语句回滚的时候,session C 几乎同时发现死锁并返回

### insert into … on duplicate key update

insert into … on duplicate key update 这个语义的逻辑是,插入一行数据,如果碰到唯一键约束,就执行后面的更新语句,如果有多个列违反了唯一性约束,就会按照索引的顺序,修改跟第一个索引冲突的行

下面的语句,由于主键和唯一索引都会冲突,但是主键 id 是先判断的,所以修改的是 id=2 的行

```mysql
insert into t values(2,2,10) on duplicate key update d=100; 
```

结果会返回 Affected rows: 2,实际上,真正更新的只有一行,只是在代码实现上,insert 和 update 都认为自己成功了

## 小结

- insert … select 是很常见的在两个表之间拷贝数据的方法; 在可重复读隔离级别下,这个语句会给 select 的表里扫描到的记录和间隙加读锁
- 如果 insert 和 select 的对象是同一个表,则有可能会造成循环写入; 这种情况下,需要引入用户临时表来做优化
- insert 语句如果出现唯一键冲突,会在冲突的唯一值上加共享的 next-key lock(S 锁); 因此,碰到由于唯一键约束导致报错后,要尽快提交或回滚事务,避免加锁时间过长