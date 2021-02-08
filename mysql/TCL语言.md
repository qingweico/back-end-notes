### 事务的特性

- 原子性（Atomicity）：一个事务不可再分割，事务中的操作要么全部执行，要么全不执行
- 一致性（Consistency）：事务必须使数据库从一个一致性状态变换到另外一个一致性状态
- 隔离性（Isolation）：一个事务的执行不能被其他事务干扰，一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的事务之间互不干扰
- 持久性（Durability）：一个事务一旦被提交，它对数据库数据的改变就是永久性的，接下来其他的操作或者数据库故障不应该对其有任何影响

### 事务的隔离级别

| 级别            | 脏读   | 不可重复度 | 幻读   |
| --------------- | ------ | ---------- | ------ |
| read uncommited | 存在   | 存在       | 存在   |
| read commited   | 不存在 | 存在       | 存在   |
| repeatable read | 不存在 | 不存在     | 存在   |
| serializable    | 不存在 | 不存在     | 不存在 |

mysql中默认的事务隔离级别是repeatable read

#### 开始事务

```mysql
set autocommit = 0;#开始事务
```

#### 查看隔离级别

```mysql
select @@transaction_isolation;
```

#### 设置隔离级别

```mysql
set SESSION TRANSACTION ISOLATION LEVEL 隔离级别;
```

