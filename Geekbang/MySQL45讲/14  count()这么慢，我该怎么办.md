### count(*) 的实现方式

MyISAM 引擎把一个表的总行数存在了磁盘上, 因此执行 count(*) 的时候会直接返回这个数, 效率很高

而 InnoDB 引擎就麻烦了, 它执行 count(*) 的时候, 需要把数据一行一行地从引擎里面读出来, 然后累积计数

因为不论是在事务支持、并发能力还是在数据安全方面, InnoDB 都优于 MyISAM

因为即使是在同一个时刻的多个查询, 由于多版本并发控制(MVCC)的原因, InnoDB 表“应该返回多少行”也是不确定的

 InnoDB 引擎中, 可重复读是它默认的隔离级别, 在代码上就是通过多版本并发控制, 也就是 MVCC 来实现的, 一行记录都要判断自己是否对这个会话可见, 因此对于 count(*) 请求来说, InnoDB 只好把数据一行一行地读出依次判断

- MyISAM 表虽然 count(*) 很快, 但是不支持事务
- show table status 命令虽然返回很快, 但是不准确
- InnoDB 表直接 count() 会遍历全表, 虽然结果准确, 但会导致性能问题

count() 是一个聚合函数, 对于返回的结果集, 一行行地判断, 如果 count 函数的参数不是 NULL, 累计值就加 1, 否则不加; 最后返回累计值

count(*)、count(主键 id) 和 count(1) 都表示返回满足条件的结果集的总行数；而 count(字段), 则表示返回满足条件的数据行里面, 参数“字段”不为 NULL 的总个数

对于 count(主键 id) 来说, InnoDB 引擎会遍历整张表, 把每一行的 id 值都取出来, 返回给 server 层; server 层拿到 id 后, 判断是不可能为空的, 就按行累加

对于 count(1) 来说, InnoDB 引擎遍历整张表, 但不取值; server 层对于返回的每一行, 放一个数字“1”进去, 判断是不可能为空的, 按行累加

count(1) 执行得要比 count(主键 id) 快; 因为从引擎返回 id 会涉及到解析数据行, 以及拷贝字段值的操作

对于 count(字段) 来说

- 如果这个“字段”是定义为 not null 的话, 一行行地从记录里面读出这个字段, 判断不能为 null, 按行累加
- 如果这个“字段”定义允许为 null, 那么执行的时候, 判断到有可能是 null, 还要把值取出来再判断一下, 不是 null 才累加

server 层要什么字段, InnoDB 就返回什么字段

按照效率排序的话, count(字段)<count(主键 id)<count(1)≈count(`*`), 所以我建议你, 尽量使用 count(`*`)