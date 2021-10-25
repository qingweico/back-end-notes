# 目录

[TOC]

## redis persistence

### RDB（Redis Database）

在指定的时间间隔内将内存中的数据集快照写入磁盘,即快照 数据恢复时直接将快照文件读入内存中

#### SNAPSHOTTING快照

```bash
dbfilename dump.rdb   # 默认持久化到dump.rdb文件中 
save 900 1            # 15分钟内保存一次即修改一次
save 300 10           # 5分钟内保存一次即修改十次
save 60 10000         # 1分钟内保存一次即修改一万次
stop-writes-on-bgsave-error yes  # 保存出错停止写入
rdbcompression yes    # 数据压缩
rdbchecksum yes       # 数据校验
dir ./                
config get dir  # 获取redis启动路径
save                  # 手动备份
```

RDB适合大规模的数据恢复,而且对数据的完整性和一致性要求不高

### AOF（Append Only File）

以日志的形式记录每个写操作,将Redis所有的写指令记录下来(不记录读操作),只许追加文件不许修改文件。相当于MySQL的脚本文件,当Redis启动时就会加载appendonly.aof文件将写指令重新执行一遍来恢复数据

#### APPEND ONLY MODE

```java
appendonly no //默认关闭 yes即代表打开aof的持久化
appendfilename "appendonly.aof"
```

```java
redis-check-aof --fix appendonly.aof //修复appendonly.aof文件
```

dump.rdb文件和appendonly.aof文件可以同时存在,当二者同时存在时优先加载appendonly.aof文件

```java
# appendfsync always //同步持久化 每当有数据发生变化时就会记录到磁盘 数据完整性好但是性能差
appendfsync everysec //出厂默认 异步操作 每秒记录数据 1s内宕机 数据丢失
# appendfsync no
```



