# 目录

[TOC]

## Transaction

一个队列中 一次性,顺序性,排他性地执行一系列命令

```bash
MULTI   # 开始事务
```

```bash
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set balance 100
QUEUED
127.0.0.1:6379> set debt 0
QUEUED
127.0.0.1:6379> 
```

将多个命令入队到事务中,接下来这些命令并不会立即执行,而是放到等待执行的事务队列中

```bash
EXEC    # 提交事务
```

```bash
DISCARD # 取消事务
```

单独的隔离操作: 事务中的所有命令都会序列化,按顺序地执行,事务在执行过程中不会被其他客户端发来的命令所打断

没有隔离级别的概念: 队列中的命令在没有提交之前都不会被实际地执行,因为在事务没有提交之前任何的指令都不会实际地执行

不能保证原子性: redis中同一事务中如果有一条命令执行失败则其后的命令仍然会被执行,没有回滚

----

redis对事务是部分支持的,如果在一次事务中如果一条命令在加入队列之前发生错误,就不会加入队列中,那么其他正常加入队列的命令都会执行失败。而在一次事务中,如果一条命令在加入队列之后执行失败,则除了该命令其他加入队列后正常执行的命令都会执行成功

```bash
 # 监视一个或多个key,如果在事务提交之前这个或这些key被其他命令所改动,那么事务将被打断并返回nil
watch
```

```bash
# 取消对所有key的监视
unwatch
```

一旦执行`EXEC`命令,之前所加的监控锁都会被取消掉

### 悲观锁

### 乐观锁

## 消息订阅发布（了解）

进程间的一种通信模式 

发送者（pub）发送消息 订阅者（sub）接受消息

先订阅后发布才能收到消息

```bash
# 订阅多个
SUBSCRIBE a b c
```

```bash
# 发布消息
PUBLISH a hello
```

```bash
# 订阅多个(*通配符)
PSUBSCRIBE a*
```

```bash
#发布消息
PUBLISH a1 hello
PUBLISH a2 HelloRedis
```



