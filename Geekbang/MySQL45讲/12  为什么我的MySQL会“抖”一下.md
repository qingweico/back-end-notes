当内存数据页跟磁盘数据页内容不一致的时候, 我们称这个内存页为“脏页”; 内存数据写入到磁盘后, 内存和磁盘上的数据页的内容就一致了, 称为“干净页”

InnoDB 在处理更新语句的时候, 只做了写日志这一个磁盘操作; 这个日志叫作 redo log(重做日志), 在更新内存写完 redo log 后, 就返回给客户端, 本次更新成功

平时执行很快的更新操作, 其实就是在写内存和日志, 而 MySQL 偶尔“抖”一下的那个瞬间, 可能就是在刷脏页(flush)

什么情况会引发数据库的 flush 过程呢

- InnoDB 的 redo log 写满了; 这时候系统会停止所有更新操作
- 对应的就是系统内存不足; 当需要新的内存页, 而内存不够用的时候, 就要淘汰一些数据页, 空出内存给别的数据页使用; 如果淘汰的是“脏页”, 就要先将脏页写到磁盘
- MySQL 认为系统“空闲”的时候; 即使是系统繁忙的时候, 也要见缝插针地找时间, 只要有机会就刷一点“脏页”
- MySQL 正常关闭的情况; 这时候, MySQL 会把内存的脏页都 flush 到磁盘上, 这样下次 MySQL 启动的时候, 就可以直接从磁盘上读数据, 启动速度会很快

InnoDB 用缓冲池(buffer pool)管理内存, 缓冲池中的内存页有三种状态：

- 第一种是, 还没有使用的；
- 第二种是, 使用了并且是干净页；
- 第三种是, 使用了并且是脏页

InnoDB 的策略是尽量使用内存, 因此对于一个长时间运行的库来说, 未被使用的页面很少

而当要读入的数据页没有在内存的时候, 就必须到缓冲池中申请一个数据页; 这时候只能把最久不使用的数据页从内存中淘汰掉：如果要淘汰的是一个干净页, 就直接释放出来复用；但如果是脏页呢, 就必须将脏页先刷到磁盘, 变成干净页后才能复用

刷脏页虽然是常态, 但是出现以下这两种情况, 都是会明显影响性能的：

- 一个查询要淘汰的脏页个数太多, 会导致查询的响应时间明显变长；
- 日志写满, 更新全部堵住, 写性能跌为 0, 这种情况对敏感业务来说, 是不能接受的

InnoDB 需要有控制脏页比例的机制, 来尽量避免上面的这两种情况

### InnoDB 刷脏页的控制策略	

innodb_io_capacity 这个参数了, 它会告诉 InnoDB 你的磁盘能力

参数 innodb_max_dirty_pages_pct 是脏页比例上限, 默认值是 75%

InnoDB 会在后台刷脏页, 而刷脏页的过程是要将内存页写入磁盘; 所以, 无论是你的查询语句在需要内存的时候可能要求淘汰一个脏页, 还是由于刷脏页的逻辑会占用 IO 资源并可能影响到了你的更新语句, 都可能是造成你从业务端感知到 MySQL“抖”了一下的原因

脏页比例是通过 Innodb_buffer_pool_pages_dirty/Innodb_buffer_pool_pages_total 得到的

MySQL 中的一个机制, 可能让你的查询会更慢：在准备刷一个脏页的时候, 如果这个数据页旁边的数据页刚好是脏页, 就会把这个“邻居”也带着一起刷掉；而且这个把“邻居”拖下水的逻辑还可以继续蔓延, 也就是对于每个邻居数据页, 如果跟它相邻的数据页也还是脏页的话, 也会被放到一起刷

在 InnoDB 中, innodb_flush_neighbors 参数就是用来控制这个行为的, 值为 1 的时候会有上述的“连坐”机制, 值为 0 时表示不找邻居, 自己刷自己的

找“邻居”这个优化在机械硬盘时代是很有意义的, 可以减少很多随机 IO; 机械硬盘的随机 IOPS 一般只有几百, 相同的逻辑操作减少随机 IO 就意味着系统性能的大幅度提升

只刷自己”, 就能更快地执行完必要的刷脏页操作, 减少 SQL 语句响应时间

在 MySQL 8.0 中, innodb_flush_neighbors 参数的默认值已经是 0 了

利用 WAL 技术, 数据库将随机写转换成了顺序写, 大大提升了数据库的性能