[TOC]

## Redis 集群

### 单点 Redis 的问题

- 数据丢失问题
- 并发能力问题
- 储存能力问题
- 故障恢复问题

### 数据丢失问题

实现 Redis 数据持久化

### 并发能力问题

搭建主从集群 实现读写分离

#### 数据同步原理

主从第一次同步是全量同步

master 判断 slave 是不是第一次同步数据

- Replication Id 简称 replid 是数据集的标记 id一致则说明是同一数据集 
- 每个master 都有唯一的 replid slave 则会继承 master 节点的 replid
- offset 偏移量 随着记录在repl_baklog中的数据增多而逐渐增大
- slave 完成同步时也会记录同步的offset
- 如果 slave 的 offset 小于 master 的 offset 说明 slave 数据落后于 master 需要更新

全量同步流程

- slave 节点请求增量同步
- master 节点判断replid 发现不一致 拒绝增量同步
- masterr 将完整内存数据生成 RDB 发送到 slave
- slave 清空本地数据 加载 master 的 RDB
- master 将 RDB 期间的写命令记录在 repl_baklog 并持续将log中的命令发送给 slave

增量同步

若 slave 重启后同步 则执行增量同步

优化 redis 主从集群

- 在master 中配置 repl-dissless-sync yes启用无磁盘复制 避免全量同步时的磁盘IO

- Redis 单节点的内存占用不要太大 减少 RDB 导致的过多的磁盘 IO

- 适当提高 repl_baklog的大小 发现 slave 宕机时尽快实现故障恢复 尽可能避免全量同步

- 限制一个 master 上 slave 节点数量 如果 slave 太多 则可以采用 主-从-从链式结构(下图所示) 减少 master 压力

  ![xxxxxx](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/xxxxxx.png)

简述全量同步和增量同步区别

- 全量同步 master 将完整内存数据生成 RDB 发送到 slave 后续命令则记录在 repl_baklog 逐个发送给 slave
- 增量同步 slave 提交自己的 offset 到 master master 获取 repl_baklog中 offset 之后的命令给 slave

什么时候执行全量同步

- slave 节点第一次连接 master 节点
- slave 节点断开时间太久 repl_baklog 中 offset 已经被覆盖时

什么时候执行增量同步

- slave 节点断开又恢复 并且在 repl_baklog 中能找到 offset 时

### 储存能力问题

搭建分片集群 利用插槽机制实现动态扩容

#### 分片集群结构

![cluster](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/cluster.png)

主从和哨兵可以解决高可用和高并发读的问题 但是依然存在两个问题

- 海量数据存储问题·
- 高并发写的问题

使用分片集群可以解决上述问题

- 集群中有多个master 每个 master 保存了不同的数据
- 每个 master 都可以有多个 slave 节点
- master 之间通过 ping 检测彼此健康状况
- 客户端请求可以访问集群任意节点 最终都会被转发到正确节点上

#### 集群搭建

redis.conf

```properties
port 6379
# 开启集群功能
cluster-enabled yes
# 集群文件的配置名称
cluster-config-file /tmp/6379/nodes.conf
# 节点心跳失败的超时时间
cluster-node-timeout 5000
# 持久化文件存放目录
dir /tmp/6379
bind 0.0.0.0
daemonize yes
# 注册的实例ip
replica-announce-ip 127.0.0.1
protected-mode no
database 1
logfile /tmp/6379/run.log
```

```properties
# redis-cli --cluster 集群操作命令
# create 创建集群
# --cluster-replicas 1 指定集群中每个 master 的副本数为 1
redis-cli --cluster create --cluster-replicas 1 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:8001 127.0.0.1:8002 127.0.0.1:8003
```

```properties
# 连接集群中的节点
redis-cli -c -p 端口号
```

#### 散列插槽

- Redis 会把每一个 master 节点映射到 0 ~ 16383 共16384 和插槽上(hash slot)
- 数据 key 不是与节点绑定 而是与插槽绑定
- redis 会根据 key 的有效部分计算插槽值
  - key 中 包含 {} 且 {} 中至少包含一个字符 则 {} 中的部分则是有效部分
  - key 中不包含 {} 则整个 key 都是有效部分
- 计算方式是利用 CRC16算法得到一个 hash 值 然后对 16384 取余 得到的结果就是 slot 值

如何将同一类数据固定的保存在同一个redis实例

- 使用相同的有效部分即可 都以某个{字段id}为前缀

### 故障恢复问题

利用 Redis 哨兵 实现健康检测和自动恢复

问题 

slave 节点宕机恢复后可以从 master 节点同步数据 那么 master 节点宕机怎么办哪

哨兵机制

#### 哨兵的作用

![sentinel](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/sentinel.png)

redis 提供了 sentinel 机制来实现从集群的自动故障恢复

- **监控** Sentinel 会不断检查您的 master 和 slave 是否正常工作
- **自动故障恢复** 如果 master 故障 Sentinel 会将一个 slave 提升为 master 当故障实例恢复后也将成为 slave
- **通知** Sentinel 充当redis客户端的服务发现来源 当集群发生故障转移时 会将最新信息推送给redis的客户端

#### 服务状态监控

Sentinel基于心跳机制检测服务状态 每隔一秒钟向集群发送 ping 命令

- 主观下线 如果某 Sentinel 节点发现某个实例未在规定时间内响应 则认为该实例主观下线
- 客观下线 若超过指定数量(quorum)的 sentinel 都认为该实例主观下线 则该实例客观下线 quorum的值最好超过sentinel 实例数量的一半

#### 选举新的 master

一旦发现 master 故障 sentinel 需要在 slave 中选择一个作为新的 master 

- 首先根据判断 slave 节点与 master 节点断开时间长短 如果超过指定值(down-after-milliseconds * 10) 则会排除该 slave 节点
- 然后判断 slave 节点的 slave-priority 值 越小优先级越高 如果是0 则永不参与选举
- 如果 slave-priority 一样 则判断 slave 节点的 offset的值 越大说明数据越新 优先级越高
- 最后判断 slave 节点的运行 id 大小 越小则优先级越高

#### 故障转移

- 选中一个 slave 后 sentinel 给 slave 节点执行命令 slave no one 让该节点成为 master

- 然后给其他节点发送命令 让其他 slave 节点成为新master节点的 slave 并开始同步数据
- 最后故障节点被 sentinel 标记为 slave 就算恢复后也将成为新 master 的 slave
