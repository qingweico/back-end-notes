# 目录

[TOC]

## redis.config

### United单位

- 配置大小单位，支持bytes，不支持bit

- 对大小写不敏感

### INCLUDES包含

```java
//redis.conf配置文件作为总闸可以包含其他配置文件
include /path/to/local.conf
include /path/to/other.conf
```

### GENERAL通用

#### Daemonize

后台运行(默认为no)

#### Pidfile

#### Port

```java
port 6379
```

#### TCP-backlog

```java
tcp-backlog 511
```

#### Timeout

```java
timeout 0 //0代表客户端不关闭连接
```

#### Bind

```java
bind 127.0.0.1 //本机
```

#### Tcp-keepalive

```java
tcp-keepalive 300 //单位为秒，代表以时间间隔进行keepalive检查 如果为0则代表不进行keepalive检查
```

#### Loglevel

```java
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel notice
```

#### Logfile

```java
logfile "" //日志文件
```

#### Syslog-enabled

```java
syslog-enabled no  //是否把日志打印到syslog中
```

#### Syslog-indent

```java
syslog-ident redis //指定syslog里的日志标志
```

#### Syslog-facility

```java
 syslog-facility local0 //指定syslog设备 local0~ocal7
```

#### Database

```java
databases 16 //
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

