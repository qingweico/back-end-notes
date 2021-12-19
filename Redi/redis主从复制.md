# 目录

[TOC]

## Replication

主从复制

配置从库不用配置主库

### 一主二仆

```bash
# 从库配置
slaveof 主库IP 主库端口
```

```bash
# 取消从库配置,使当前数据库停止与其他数据库的同步,转化为主数据库
slaveof no one
```

```bash
# 主库
127.0.0.1: 6379> info replication  
# Replication
role: master
connected_slaves: 0
master_replid: 7f39ff900c939bf7795372bf8160ff31aa0fd26e
master_replid2: f97aced19f0231382f5c223b557c22d6a12d5e70
master_repl_offset: 0
second_repl_offset: 1
repl_backlog_active: 0
repl_backlog_size: 1048576
repl_backlog_first_byte_offset: 0
repl_backlog_histlen: 0
```

```bash
# 从库一
[root@centos bin]# ./redis-server ./6380.conf
[root@centos bin]# ./redis-cli -p 6380
# 查看从库的信息
127.0.0.1: 6380> info replication
# Replication
role: master
connected_slaves: 0
master_replid: 6bd8662e4fec3c530d4f5c2ecc9d4110b9da90a9
master_replid2: 0000000000000000000000000000000000000000
master_repl_offset: 0
second_repl_offset: -1
repl_backlog_active: 0
repl_backlog_size: 1048576
repl_backlog_first_byte_offset: 0
repl_backlog_histlen: 0
# 从库配置一
127.0.0.1: 6380> slaveof 127.0.0.1 6379
OK
# 再次查看从库信息
127.0.0.1: 6380> info replication
# Replication
role: slave
master_host: 127.0.0.1
master_port: 6379
master_link_status: up
master_last_io_seconds_ago: 6
master_sync_in_progress: 0
slave_repl_offset: 1608
slave_priority: 100
slave_read_only: 1
connected_slaves: 0
master_replid: 0a026d10eda541c2cb2e00b21099ad5a830881d2
master_replid2: 0000000000000000000000000000000000000000
master_repl_offset: 1608
second_repl_offset: -1
repl_backlog_active: 1
repl_backlog_size: 1048576
repl_backlog_first_byte_offset: 1
repl_backlog_histlen: 1608
```

```bash
# 从库二
[root@centos bin]# ./redis-server ./6381.conf
[root@centos bin]# ./redis-cli
# 配置从库二
127.0.0.1: 6379> slaveof 127.0.0.1 6379
OK Already connected to specified master
# 再次查看从库信息    
127.0.0.1: 6381> info replication
# Replication
role: slave
master_host: 127.0.0.1
master_port: 6379
master_link_status: up
master_last_io_seconds_ago: 6
master_sync_in_progress: 0
slave_repl_offset: 1790
slave_priority: 100
slave_read_only: 1
connected_slaves: 0
master_replid: 0a026d10eda541c2cb2e00b21099ad5a830881d2
master_replid2: 0000000000000000000000000000000000000000
master_repl_offset: 1790
second_repl_offset: -1
repl_backlog_active: 1
repl_backlog_size: 1048576
repl_backlog_first_byte_offset: 29
repl_backlog_histlen: 1762
```

```bash
# 再次查看主库信息
127.0.0.1: 6379> info replication
# Replication
role: master
connected_slaves: 2
slave0: ip=127.0.0.1,port=6380,state=online,offset=417,lag=1 # 从库一
slave1: ip=127.0.0.1,port=6381,state=online,offset=417,lag=1 # 从库二
master_replid: 0a026d10eda541c2cb2e00b21099ad5a830881d2
master_replid2: 0000000000000000000000000000000000000000
master_repl_offset: 417
second_repl_offset: -1
repl_backlog_active: 1
repl_backlog_size: 1048576
repl_backlog_first_byte_offset: 1
repl_backlog_histlen: 417
```

主库可读也可写(主要写数据),但是从库只可以读数据

```bash
127.0.0.1: 6381> set k1 v1
(error) READONLY You can't write against a read only replica.
```

从库会备份主库所有的数据(并不是只备份配置从库之后时间段的数据)

当主库掉线时(使用shutdown命令,在使用exit命令  直接使用exit命令不行),并不会消除和从库的关系,当主库重新上线时依然是之前从库的主库

当从库掉线时,会消除和主库的关系以及之前所有备份的数据(注意备份文件dump.rdb和aof的影响),即当从库重新上线时为master,需要手动重新配置从库,而且此时会复制主库之前所有的数据

```bash
slaveof 127.0.0.1 6379
```

### 薪火相传

从库既可以作为上一个主库的从库,也可以作为下一个从库的主库

```bash
# 主库
127.0.0.1: 6379>
# 6379的从库 6381的主库
127.0.0.1: 6380> slaveof 127.0.0.1 6379
# 6380的从库
127.0.0.1: 6381> slaveof 127.0.0.1 6380
```

### 反客为主

```bash
# 当主库下线时
127.0.0.1: 6379> shutdown
# 成为主库
127.0.0.1: 6380> slaveof no one 
# 6380的从库
127.0.0.1: 6381> slaveof 127.0.0.1 6380
```

总结: 当slaveof启动成功连接到master后会发送一个sync命令,master在接收到命令后会启动后台的存盘进程,同时收集所有接收到用于修改数据集的命令,在后台进程执行完毕之后,master将传送整个数据文件到slave,已完成一次完全同步

全量复制: 在slave服务接收到数据库文件之后,将其存盘并加载到内存中

增量复制: master继续将新的所有收集到的命令一次传给slave完成同步

只要重新连接master,一次完全同步(增量复制)将被自动执行

## 哨兵模式(sentinel)

新建sentinel.conf文件

sentinel monitor 被监控数据库的名字 监控主机的ip 票数(最终票数大于票数的数据库成为主库)

```bash
# 启动哨兵模式
./redis-sentinel ./sentinel.conf
```

```bash
3600: X 17 Jul 2020 13: 57: 16.215 # Sentinel ID is 0d755d0aeb17d57bdcfac3e5990575f4986e222e
3600: X 17 Jul 2020 13: 57: 16.215 # +monitor master post6379 127.0.0.1 6379 quorum 1
3600: X 17 Jul 2020 13: 57: 16.217 * +slave slave 127.0.0.1: 6381 127.0.0.1 6381 @ post6379 127.0.0.1 6379
3600: X 17 Jul 2020 13: 57: 16.219 * +slave slave 127.0.0.1: 6380 127.0.0.1 6380 @ post6379 127.0.0.1 6379
```

```bash
# 主库下线
127.0.0.1: 6379> SHUTDOWN
not connected> exit
```

```bash
3600: X 17 Jul 2020 14: 00: 14.585 # +sdown master post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 14.585 # +odown master post6379 127.0.0.1 6379 #quorum 1/1
3600: X 17 Jul 2020 14: 00: 14.585 # +new-epoch 1
3600: X 17 Jul 2020 14: 00: 14.585 # +try-failover master post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 14.586 # +vote-for-leader 0d755d0aeb17d57bdcfac3e5990575f4986e222e 1
3600: X 17 Jul 2020 14: 00: 14.586 # +elected-leader master post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 14.586 # +failover-state-select-slave master post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 14.662 # +selected-slave slave 127.0.0.1: 6381 127.0.0.1 6381 @ post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 14.662 * +failover-state-send-slaveof-noone slave 127.0.0.1: 6381 127.0.0.1 6381 @ post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 14.729 * +failover-state-wait-promotion slave 127.0.0.1: 6381 127.0.0.1 6381 @ post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 14.915 # +promoted-slave slave 127.0.0.1: 6381 127.0.0.1 6381 @ post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 14.915 # +failover-state-reconf-slaves master post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 14.965 * +slave-reconf-sent slave 127.0.0.1: 6380 127.0.0.1 6380 @ post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 15.936 * +slave-reconf-inprog slave 127.0.0.1: 6380 127.0.0.1 6380 @ post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 15.936 * +slave-reconf-done slave 127.0.0.1: 6380 127.0.0.1 6380 @ post6379 127.0.0.1 6379
3600: X 17 Jul 2020 14: 00: 15.990 # +failover-end master post6379 127.0.0.1 6379
# 选举6381为主库
3600: X 17 Jul 2020 14: 00: 15.990 # +switch-master post6379 127.0.0.1 6379 127.0.0.1 6381
3600: X 17 Jul 2020 14: 00: 15.991 * +slave slave 127.0.0.1: 6380 127.0.0.1 6380 @ post6379 127.0.0.1 6381
3600: X 17 Jul 2020 14: 00: 15.991 * +slave slave 127.0.0.1: 6379 127.0.0.1 6379 @ post6379 127.0.0.1 6381
3600: X 17 Jul 2020 14: 00: 46.001 # +sdown slave 127.0.0.1: 6379 127.0.0.1 6379 @ post6379 127.0.0.1 6381

```

```bash
# 主库变成从库
127.0.0.1: 6379> info replication
# Replication
role: slave
master_host: 127.0.0.1
master_port: 6380
master_link_status: up
master_last_io_seconds_ago: 0
master_sync_in_progress: 0
slave_repl_offset: 28439
slave_priority: 100
slave_read_only: 1
connected_slaves: 0
master_replid: 57f61dc1ed803e1b709eed6eaf5f10935786c059
master_replid2: 0000000000000000000000000000000000000000
master_repl_offset: 28439
second_repl_offset: -1
repl_backlog_active: 1
repl_backlog_size: 1048576
repl_backlog_first_byte_offset: 26779
repl_backlog_histlen: 1661
```

```bash
# 从库变成主库
127.0.0.1: 6380> info replication
# Replication
role: master
connected_slaves: 2
slave0: ip=127.0.0.1,port=6381,state=online,offset=65784,lag=1
slave1: ip=127.0.0.1,port=6379,state=online,offset=65784,lag=1
master_replid: 57f61dc1ed803e1b709eed6eaf5f10935786c059
master_replid2: e64c672ae3237d6d05788b4781de8007d6f65b5d
master_repl_offset: 65784
second_repl_offset: 20617
repl_backlog_active: 1
repl_backlog_size: 1048576
repl_backlog_first_byte_offset: 1
repl_backlog_histlen: 65784
```

同一组sentinel可以同时监控多个master

复制的缺点: 由于所有的写操作都是在master上,然后同步更新到slave上,所以从master到slave会有一定的延迟,当slave很多时,会使延迟加重