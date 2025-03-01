### 重要的日志模块：redo log

WAL 技术, WAL 的全称是 Write-Ahead Logging, 它的关键点就是先写日志, 再写磁盘

当有一条记录需要更新的时候, InnoDB 引擎就会先把记录写到 redo log 

里面, 并更新内存, 这个时候更新就算完成了; 同时, InnoDB 引擎会在适当的时候, 将这个操作记录更新到磁盘里面, 而这个更新往往是在系统比较空闲的时候做

redo log 是循环写的，空间固定会用完；binlog 是可以追加写入的（文件写到一定大小后会切换到下一个，并不会覆盖以前的日志）

![img](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/16a7950217b3f0f4ed02db5db59562a7.png)

InnoDB 的 redo log 是固定大小的, 比如可以配置为一组 4 个文件, 每个文件的大小是 1GB, 那么这块“粉板”总共就可以记录 4GB 的操作; 从头开始写, 写到末尾就又回到开头循环写

**有了 redo log, InnoDB 就可以保证即使数据库发生异常重启, 之前提交的记录都不会丢失, 这个能力称为 crash-safe**

### 重要的日志模块：binlog

redo log 是 InnoDB 引擎特有的日志, 而 Server 层也有自己的日志, 称为 binlog(归档日志)

最开始 MySQL 里并没有 InnoDB 引擎; MySQL 自带的引擎是 MyISAM, 但是 MyISAM 没有 crash-safe 的能力, binlog 日志只能用于归档; 而 InnoDB 是另一个公司以插件形式引入 MySQL 的, 既然只依靠 binlog 是没有 crash-safe 能力的, 所以 InnoDB 使用另外一套日志系统——也就是 redo log 来实现 crash-safe 能力

- redo log 是 InnoDB 引擎特有的；binlog 是 MySQL 的 Server 层实现的, 所有引擎都可以使用
- redo log 是物理日志, 记录的是“在某个数据页上做了什么修改”；binlog 是逻辑日志, 记录的是这个语句的原始逻辑, 比如“给 ID=2 这一行的 c 字段加 1 ”
- redo log 是循环写的, 空间固定会用完；binlog 是可以追加写入的; “追加写”是指 binlog 文件写到一定大小后会切换到下一个, 并不会覆盖以前的日志

redo log不是记录数据页“更新之后的状态”, 而是记录这个页 “做了什么改动”;  binlog有两种模式, statement 格式的话是记sql语句,  row格式会记录行的内容, 记两条, 更新前和更新后都有

### 两阶段提交

![img](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/2e5bff4910ec189fe1ee6e2ecc7b4bbe.png)

**为什么日志需要“两阶段提交”**

由于 redo log 和 binlog 是两个独立的逻辑，如果不用两阶段提交，要么就是先写完 redo log 再写 binlog，或者采用反过来的顺序，这两种方式都会有问题

如果不使用“两阶段提交”, 那么数据库的状态就有可能和用它的日志恢复出来的库的状态不一致

redo log 和 binlog 都可以用于表示事务的提交状态, 而两阶段提交就是让这两个状态保持逻辑上的一致

redo log 用于保证 crash-safe 能力; innodb_flush_log_at_trx_commit 这个参数设置成 1 的时候, 表示每次事务的 redo log 都直接持久化到磁盘; 建议你设置成 1, 这样可以保证 MySQL 异常重启之后数据不丢失; 

sync_binlog 这个参数设置成 1 的时候, 表示每次事务的 binlog 都持久化到磁盘; 建议你设置成 1, 这样可以保证 MySQL 异常重启之后 binlog 不丢失

两阶段提交是跨系统维持数据逻辑一致性时常用的一个方案