MySQL 建立连接的过程，成本是很高的。除了正常的网络连接三次握手外，还需要做登录权限判断和获得这个连接的数据读写权限

max_connections 参数，用来控制一个 MySQL 实例同时存在的连接数的上限，超过这个值，系统就会拒绝接下来的连接请求，并报错提示“Too many connections”

### 第一种方法：先处理掉那些占着连接但是不工作的线程

max_connections 的计算，不是看谁在 running，是只要连着就占用一个计数位置。对于那些不需要保持的连接，我们可以通过 kill connection 主动踢掉

设置 wait_timeout 参数表示的是，一个线程空闲 wait_timeout 这么多秒之后，就会被 MySQL 直接断开连接

```mysql
# 当在processlist中查看到sleep的事务, 想要kill 掉, 但又不知道它之前是否有修改数据以此导致回滚, 可以查看innodb_trx表, 其中有一个字段trx_rows_modified代表该事务修改的行数, 如果值为0, 那么就可以放心kill 了
select * from information_schema.innodb.trx;
```

如果是连接数过多，你可以优先断开事务外空闲太久的连接；如果这样还不够，再考虑断开事务内空闲太久的连接

从服务端断开连接使用的是 kill connection + id 的命令， 一个客户端处于 sleep 状态时，它的连接被服务端主动断开后，这个客户端并不会马上知道。直到客户端在发起下一个请求的时候，才会收到这样的报错“ERROR 2013 (HY000): Lost connection to MySQL server during query”

从数据库端主动断开连接可能是有损的，尤其是有的应用端收到这个错误后，不重新连接，而是直接用这个已经不能用的句柄重试查询。这会导致从应用端看上去，“MySQL 一直没恢复”

从数据库端主动断开连接可能是有损的，尤其是有的应用端收到这个错误后，不重新连接，而是直接用这个已经不能用的句柄重试查询。这会导致从应用端看上去，“MySQL 一直没恢复”

### 第二种方法：减少连接过程的消耗

让数据库跳过权限验证阶段

跳过权限验证的方法是：重启数据库，并使用–skip-grant-tables 参数启动。这样，整个 MySQL 会跳过所有的权限验证阶段，包括连接过程和语句执行过程在内

在 MySQL 8.0 版本里，如果你启用–skip-grant-tables 参数，MySQL 会默认把 --skip-networking 参数打开，表示这时候数据库只能被本地的客户端连接

### 慢查询性能问题

在 MySQL 中，会引发性能问题的慢查询，大体有以下三种可能：

- 索引没有设计好；

- SQL 语句没写好；
- MySQL 选错了索引

1 紧急创建索引来解决。MySQL 5.6 版本以后，创建索引都支持 Online DDL 了 ，最高效的做法就是直接执行 alter table 语句

2 MySQL 5.7 提供了 query_rewrite 功能，可以把输入的一种语句改写成另外一种模式

3 应急方案就是给这个语句加上 force index。同样地，使用查询重写功能，给原来的语句加上 force index，也可以解决这个问题