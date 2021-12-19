# 目录

[TOC]

## redis.config

### United单位

- 配置大小单位,支持byte,不支持bit

- 对大小写不敏感

### INCLUDES包含

```shell
# redis.conf配置文件作为总闸可以包含其他配置文件
include /path/to/local.conf
include /path/to/other.conf
```

### GENERAL通用

#### Daemonize

后台运行(默认为no)

#### Pidfile

#### Port

```shell
port 6379
```

#### TCP-backlog

```shell
tcp-backlog 511
```

#### Timeout

```shell
 # 0代表客户端不关闭连接
timeout 0
```

#### Bind

```shell
# 本机
bind 127.0.0.1 
```

#### Tcp-keepalive

```shell
# 单位为秒,代表以时间间隔进行keepalive检查 如果为0则代表不进行keepalive检查
tcp-keepalive 300
```

#### Loglevel

```shell
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel notice
```

#### Logfile

```shell
# 日志文件
logfile "" 
```

#### Syslog-enabled

```shell
# 是否把日志打印到syslog中
syslog-enabled no  
```

#### Syslog-indent

```shell
# 指定syslog里的日志标志
syslog-ident redis
```

#### Syslog-facility

```shell
# 指定syslog设备 local0~ocal7
syslog-facility local0
```

#### Database

```shell
databases 16 /
```

### SNAPSHOTTING快照

### APPEND ONLY MODE

### REPLICATION复制

### SECURITY安全

### LIMIT（限制）

#### Maxclients

```java
maxclients 10000
```

#### Maxmemory

```java
maxmemory <bytes>
```

#### Maxmemory-policy

```java
maxmemory-policy noeviction
```

#### Maxmemory-samples

```java
maxmemory-samples 5
```

