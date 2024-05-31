# RocketMQ

[TOC]

## 安装(阿里云Centos7.x环境)

```bash
wget https://archive.apache.org/dist/rocketmq/4.7.1/rocketmq-all-4.7.1-bin-release.zip -P /opt
cd /opt && unzip rocketmq-all-4.7.1-bin-release.zip
cd rocketmq-all-4.7.1-bin-release
```

## 修改配置文件(修改 JVM 内存设置)

```bash
cd bin
vim runserver.sh
# JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
```

```bash
cd bin
vim runbroker.sh
#JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m"
```

## 启动

```bash
# Start Name Server
nohup sh bin/mqnamesrv &
# 查看启动日志
tail -f ~/logs/rocketmqlogs/namesrv.log
# Start Broker
nohup sh bin/mqbroker -n localhost:9876 &
# 查看启动日志
tail -f ~/logs/rocketmqlogs/broker.log 
```

若启动mq失败,可以使用命令`cat nohup.out`查看具体原因

使用云服务器连接rocketmq若显示内网连接异常则修改conf文件夹下broker.conf文件

```shell
# 添加以下内容
namesrvAddr=公网ip:9876
brokerIP1=公网ip
autoCreateTopicEnable=true
# 以加载配置文件方式重新启动broker
nohup sh bin/mqbroker -c conf/broker.conf &
```

## 关闭

```shell
cd bin
./mqshutdown broker
./mqshutdown namesrv
```

## RocketMQ Dashboard

```shell
# 下载RocketMQ Dashboard
# Git Repo
# https://github.com/apache/rocketmq-externals
wget https://dist.apache.org/repos/dist/release/rocketmq/rocketmq-dashboard/1.0.0/rocketmq-dashboard-1.0.0-source-release.zip -P /opt
# 打包
# 启动
nohup java -jar rocketmq-dashboard-1.0.0.jar --server.port=8888 --rocketmq.config.namesrvAddr=127.0.0.1:9876 > dashboard.1og & 
```

​	
