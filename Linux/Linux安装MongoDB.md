# 目录

[TOC]

## 安装配置mongodb

centos7.x

```bash
# 下载
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-5.0.3-rc0.tgz -P /opt
# 解压
cd /opt && tar -zxvf mongodb-linux-x86_64-rhel70-5.0.3-rc0.tgz -C  /usr/local
# 重命名
cd /usr/local && mv mongodb-linux-x86_64-rhel70-5.0.3-rc0 mongodb
# 其他版本下载 -> https://www.mongodb.com/try/download/community
```

```bash
# 配置环境变量
vim /etc/profile
# Set MongoDB Configuration
export PATH=/usr/local/mongodb/bin:$PATH
source /etc/profile
# 查看MongoDB版本
mongo --version
```

```bash
cd /usr/local/mongodb
# 用于储存数据库文件数据
mkdir data/db -p
mkdir data/logs
# 用于储存日志
cd data/logs
touch mongodb.log
```

```bash
# 在mongodb根目下创建核心配置文件
vim mongodb.conf
# ========================================
# 端口号
port=27017
# 数据库文件位置
dbpath=/usr/local/mongodb/data/db
# 日志文件位置
logpath=/usr/local/mongodb/data/logs/mongodb.log

# 以追加日志的形式记录
logappend=true
# 滤掉无用日志信息,若需要调试使用请设置为false
quiet=true
# 以后台方式运行
fork=true
# 最大同时连接数
maxConns=100
# 不启用验证权限
noauth=true
# 启用用户账号权限
# auth=true
# 开启日志,默认true
journal=true
# 提供外网访问,不对ip进行绑定,原理同redis的bindip
bind_ip = 0.0.0.0
# ========================================
```

```bash
# 启动mongodb
mongod -f /usr/local/mongodb/mongodb.conf
```

## 创建mongodb用户账号权限

修改配置文件mongodb.conf

```bash
# 进入mongo控制台
mongo
# 切换到admin库
use admin
# 创建用户 账号和密码为root root 角色为root
db.createUser({user: "root", pwd: "root", roles: ["root"]})
# root用户需要在admin数据库中认证(MongoDB的用户权限和数据库是绑定的)
db.auth("root", "root")
# 登录
mongo -u 'root' -p 'root'
# 查看用户
show users
# 查看所有的数据库
show dbs
# 创建数据库
use gy
# 添加一条数据
db.gy.insert({"now": Date()})

```

```bash
# 不启用验证权限
# noauth=true
# 启用用户账号权限
# auth=true
# 重启mongodb
```

| 角色名称             | 说明                                                         |
| :------------------- | :----------------------------------------------------------- |
| Read                 | 允许用户读取指定数据库                                       |
| readWrite            | 允许用户读写指定数据库                                       |
| dbAdmin              | 允许用户在指定数据库中执行管理函数,如索引创建、删除,查看统计或访问system.profile |
| userAdmin            | 允许用户向system.users集合写入,可以找指定数据库里创建、删除和管理用户 |
| clusterAdmin         | 只在admin数据库中可用, 赋予用户所有分片和复制集相关函数的管理权限 |
| readAnyDatabase      | 只在admin数据库中可用, 赋予用户所有数据库的读权限            |
| readWriteAnyDatabase | 只在admin数据库中可用, 赋予用户所有数据库的读写权限          |
| userAdminAnyDatabase | 只在admin数据库中可用, 赋予用户所有数据库的userAdmin权限     |
| dbAdminAnyDatabase   | 只在admin数据库中可用, 赋予用户所有数据库的dbAdmin权限       |
| root                 | 只在admin数据库中可用, 超级账号,超级权限                     |

