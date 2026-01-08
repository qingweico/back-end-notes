只要 redo log 和 binlog 保证持久化到磁盘,就能确保 MySQL 异常重启后,数据可以恢复

### binlog 的写入机制

事务执行过程中,先把日志写到 binlog cache,事务提交的时候,再把 binlog cache 写到 binlog 文件中; 一个事务的 binlog 是不能被拆开的,因此不论这个事务多大,也要确保一次性写入

系统给 binlog cache 分配了一片内存,每个线程一个,参数 binlog_cache_size 用于控制单个线程内 binlog cache 所占内存的大小; 如果超过了这个参数规定的大小,就要暂存到磁盘

事务提交的时候,执行器把 binlog cache 里的完整事务写入到 binlog 中,并清空 binlog cache

每个线程有自己 binlog cache,但是共用同一份 binlog 文件

- write,指的就是指把日志写入到文件系统的 page cache,并没有把数据持久化到磁盘,所以速度比较快
- fsync,才是将数据持久化到磁盘的操作; 一般情况下,我们认为 fsync 才占磁盘的 IOPS

write 和 fsync 的时机,是由参数 sync_binlog 控制的

- sync_binlog=0 的时候,表示每次提交事务都只 write,不 fsync;
- sync_binlog=1 的时候,表示每次提交事务都会执行 fsync;
- sync_binlog=N(N>1) 的时候,表示每次提交事务都 write,但累积 N 个事务后才 fsync

### redo log 的写入机制

事务在执行过程中,生成的 redo log 是要先写到 redo log buffer 的

如果事务执行期间 MySQL 发生异常重启,那这部分日志就丢了; 由于事务并没有提交,所以这时日志丢了也不会有损失

事务还没提交的时候,redo log buffer 中的部分日志会有可能被持久化到磁盘

redo log 可能存在的三种状态

- 存在 redo log buffer 中,物理上是在 MySQL 进程内存中
- 写到磁盘 (write),但是没有持久化(fsync),物理上是在文件系统的 page cache 里面
- 持久化到磁盘,对应的是 hard disk

为了控制 redo log 的写入策略,InnoDB 提供了 innodb_flush_log_at_trx_commit 参数,它有三种可能取值

- 设置为 0 的时候,表示每次事务提交时都只是把 redo log 留在 redo log buffer 中 ;
- 设置为 1 的时候,表示每次事务提交时都将 redo log 直接持久化到磁盘;
- 设置为 2 的时候,表示每次事务提交时都只是把 redo log 写到 page cache

InnoDB 有一个后台线程,每隔 1 秒,就会把 redo log buffer 中的日志,调用 write 写到文件系统的 page cache,然后调用 fsync 持久化到磁盘

事务执行中间过程的 redo log 也是直接写在 redo log buffer 中的,这些 redo log 也会被后台线程一起持久化到磁盘; 也就是说,一个没有提交的事务的 redo log,也是可能已经持久化到磁盘的

除了后台线程每秒一次的轮询操作外,还有两种场景会让一个没有提交的事务的 redo log 写入到磁盘中

- redo log buffer 占用的空间即将达到 innodb_log_buffer_size 一半的时候,后台线程会主动写盘(由于这个事务并没有提交,所以这个写盘动作只是 write,而没有调用 fsync,也就是只留在了文件系统的 page cache)
- 并行的事务提交的时候,顺带将这个事务的 redo log buffer 持久化到磁盘

时序上 redo log 先 prepare, 再写 binlog,最后再把 redo log commit

如果把 innodb_flush_log_at_trx_commit 设置成 1,那么 redo log 在 prepare 阶段就要持久化一次,因为有一个崩溃恢复逻辑是要依赖于 prepare 的 redo log,再加上 binlog 来恢复的

每秒一次后台轮询刷盘,再加上崩溃恢复这个逻辑,InnoDB 就认为 redo log 在 commit 的时候就不需要 fsync 了,只会 write 到文件系统的 page cache 中就够了

MySQL 的"双 1"配置,指的就是 sync_binlog 和 innodb_flush_log_at_trx_commit 都设置成 1; 也就是说,一个事务完整提交前,需要等待两次刷盘,一次是 redo log(prepare 阶段),一次是 binlog

### 组提交(group commit)机制

日志逻辑序列号(log sequence number,LSN)

LSN 是单调递增的,用来对应 redo log 的一个个写入点; 每次写入长度为 length 的 redo log, LSN 的值就会加上 length

LSN 也会写到 InnoDB 的数据页中,来确保数据页不会被多次执行重复的 redo log

一次组提交里面,组员越多,节约磁盘 IOPS 的效果越好; 但如果只有单线程压测,那就只能老老实实地一个事务对应一次持久化操作了

在并发更新场景下,第一个事务写完 redo log buffer 以后,接下来这个 fsync 越晚调用,组员可能越多,节约 IOPS 的效果就越好

为了让一次 fsync 带的组员更多,MySQL 有一个很有趣的优化: 拖时间

<img src="https://static001.geekbang.org/resource/image/98/51/98b3b4ff7b36d6d72e38029b86870551.png" alt="img" />

binlog 是分成两步的

- 先把 binlog 从 binlog cache 中写到磁盘上的 binlog 文件
- 调用 fsync 持久化

binlog 也可以组提交了

如果有多个事务的 binlog 已经写完了,也是一起持久化的,这样也可以减少 IOPS 的消耗

如果你想提升 binlog 组提交的效果,可以通过设置 binlog_group_commit_sync_delay 和 binlog_group_commit_sync_no_delay_count 来实现

- binlog_group_commit_sync_delay 参数,表示延迟多少微秒后才调用 fsync;
- binlog_group_commit_sync_no_delay_count 参数,表示累积多少次以后才调用 fsync

这两个条件是或的关系,也就是说只要有一个满足条件就会调用 fsync

WAL 机制主要得益于两个方面:

- redo log 和 binlog 都是顺序写,磁盘的顺序写比随机写速度要快;
- 组提交机制,可以大幅度降低磁盘的 IOPS 消耗

如果你的 MySQL 现在出现了性能瓶颈,而且瓶颈在 IO 上,可以通过哪些方法来提升性能呢

- 设置 binlog_group_commit_sync_delay 和 binlog_group_commit_sync_no_delay_count 参数,减少 binlog 的写盘次数; 这个方法是基于"额外的故意等待"来实现的,因此可能会增加语句的响应时间,但没有丢失数据的风险
- 将 sync_binlog 设置为大于 1 的值(比较常见是 100~1000); 这样做的风险是,主机掉电时会丢 binlog 日志
- 将 innodb_flush_log_at_trx_commit 设置为 2; 这样做的风险是,主机掉电的时候会丢数据