[TOC]



binlog 可以用来归档,也可以用来做主备同步,在 MySQL 的各种高可用方案上扮演了重要角色

## MySQL 主备的基本原理
主库 A -> 读写都直接访问节点 A
从库 B -> 将 A 的更新都同步过来, 本地执行 保证节点 B 和 A 的数据是相同的

-- 执行反转

主库 B
从库 A
执行上面相同的流程
-- 
无论是什么情形 都需要将备库设置为只读模式(readonly)
- 防止误操作
- 防止切换逻辑有 bug,比如切换过程中出现双写,造成主备不一致
- 可以用 readonly 状态,来判断节点的角色
问题： readonly 设置对超级 (super) 权限用户是无效的,而用于同步更新的线程,就拥有超级权限
下面是主库和从库之间内部的执行流程
- 1 主库接收到客户端的更新请求后,
- 2 备库 B 跟主库 A 之间维持了一个长连接;主库 A 内部有一个线程,专门用于服务备库 B 的这个长连接
  - 在备库 B 上通过 change master 命令,设置主库 A 的 IP、端口、用户名、密码,以及要从哪个位置开始请求 binlog,这个位置包含文件名和日志偏移量
  - 在备库 B 上执行 start slave 命令,这时候备库会启动两个线程, 一个是 io_thread, 一个是sql_thread 其中前者负责与主库建立连接
  - 主库 A 校验完用户名、密码后,开始按照备库 B 传过来的位置,从本地读取 binlog,发给 B
  - 备库 B 拿到 binlog 后,写到本地文件,称为中转日志(relay log)
  - sql_thread 读取中转日志(后续由于多线程复制方案的引入,sql_thread 演化成为了多个线程),解析出日志里的命令,并执行

## 快速搭建环境(Mac + Docker Desktop)

目标架构

- mysql-master   (MySQL 8.0.x)
- mysql-slave    (MySQL 8.0.x)

### 准备网络

```sql
docker network create mysql-net
```

### 配置 master

```sql
mkdir -p ~/mysql/master/conf
vim ~/mysql/master/conf/my.cnf

[mysqld]
server-id=1
log-bin=mysql-bin
binlog_format=STATEMENT

# 启动 master
docker run -d \
  --name mysql-master \
  --network mysql-net \
  -p 3307:3306 \
  -v ~/mysql/master/conf/my.cnf:/etc/mysql/conf.d/my.cnf \
  -e MYSQL_ROOT_PASSWORD=123456 \
  mysql:8.0
  
# 创建复制账号
docker exec -it mysql-master mysql -uroot -p123456CREATE USER 'repl'@'%' IDENTIFIED BY '123456';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
FLUSH PRIVILEGES;
SHOW MASTER STATUS;

```

### 配置 salve

```sql
mkdir -p ~/mysql/slave/conf
vim ~/mysql/slave/conf/my.cnf


[mysqld]
server-id=2
relay-log=relay-bin
read_only=ON
binlog_format=STATEMENT

# 启动 slave
docker run -d \
  --name mysql-slave \
  --network mysql-net \
  -p 3308:3306 \
  -v ~/mysql/slave/conf/my.cnf:/etc/mysql/conf.d/my.cnf \
  -e MYSQL_ROOT_PASSWORD=123456 \
  mysql:8.0
  
# 配置主从复制(重要配置)

docker exec -it mysql-slave mysql -uroot -p123456

CHANGE MASTER TO
	# 使用容器名
  MASTER_HOST='mysql-master',
  MASTER_USER='repl',
  MASTER_PASSWORD='123456',
  # 从上面的 show master status; 命令获取
  MASTER_LOG_FILE='mysql-bin.000001',
  # 从上面的 show master status; 命令获取
  MASTER_LOG_POS=xxx,
  GET_MASTER_PUBLIC_KEY = 1;

START SLAVE;
SHOW SLAVE STATUS\G;
```

启动完成

```shell
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```



## binlog 三种格式的对比

- statement
- row
- mixed(前两种格式的混合)

binlog 使用 statement 这种格式存在的问题

- 由于 statement 格式下,记录到 binlog 里的是语句原文

- 删除语句where中有多个索引并且带limit可能会导致不同数据库之间,所选用的索引不一致而导致选到的数据不一致
- 带有 limit 的 delete 且 **没有 ORDER BY** 的情况下是非确定性操作,所选择的行顺序取决于
  - 数据文件的物理存储顺序
  - 索引访问顺序
  - 查询优化器执行计划
- 所以不同服务器、不同数据页分布,可能选到不同的行

row 格式的内容

- row 格式的 binlog 里没有了 SQL 语句的原文,而是替换成了两个 event, 而且binlog 里面记录的是,真实删除行的主键 id,所以不会有主备删除不同行的问题

为什么会有 mixed 格式的 binlog

- 因为这两种格式都有优缺点
- 有些 statement 格式的 binlog 可能会导致主备不一致,所以要使用 row 格式
- 但 row 格式的缺点是,很占空间,当 delete 大量数据时, statement 的话就是一个 SQL 语句被记录到 binlog 中,而如果是row 格式的 binlog,每条记录都会写日志, 不仅会占用更大的空间,同时也要耗费 IO 资源,影响执行速度
- 所以使用折中方案,mixed 格式的意思是,MySQL 自己会判断这条 SQL 语句是否可能引起主备不一致,如果有可能,就用 row 格式,否则就用 statement 格式

## 数据恢复

虽然 row 格式有上述缺点, 但是 row 格式的binlog还是使用最多的,因为可以恢复数据

```sql
insert into t values(10,10, now());
```

使用binlog恢复数据时不能直接将binlog将其中的语句等拷贝出来直接执行,因为某些数据可能是基于上下文执行的(比如上面的时间函数),直接执行的结果很可能是错误的;标准做法是,用 mysqlbinlog 工具解析出来,然后把解析结果整个发给 MySQL 执行

## 循环复制问题

双 M 结构存在的问题

从节点执行 relay log 后生成 binlog,当成为 master 时会被之前的 master 执行(现在是slave),从而会不断地循环执行这个更新语句

解决

- 规定两个库的 server id 必须不同,如果相同,则它们之间不能设定为主备关系
- 一个备库接到 binlog 并在重放的过程中,生成与原 binlog 的 server id 相同的新的 binlog
- 每个库在收到从自己的主库发过来的日志后,先判断 server id,如果跟自己的相同,表示这个日志是自己生成的,就直接丢弃这个日志
