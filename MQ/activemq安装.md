## ActiveMQ

### 安装

```bash
# 下载activemq至/opt
wget https://archive.apache.org/dist/activemq/5.16.0/apache-activemq-5.16.0-bin.tar.gz -P /opt
# 解压
tar -zxvf /opt/apache-activemq-5.16.0-bin.tar.gz -C /usr/local
```

### 配置

```bash
cd apache-activemq-5.16.0
# 修改host 127.0.0.1 -> 0.0.0.0
vim conf/jetty.xml
```

### 启动

```bash
### activemq安装的根目录
# 启动activemq
bin/activemq start
# 重新加载
bin/activemq reload
# 停止
bin/activemq stop
# 启动异常 more data/activemq.log
# java.net.URISyntaxException: Illegal character in hostname at index 11
# 原因 : 主机名称带下划线
vi /etc/hostname
vi /etc/hosts
hostnamectl set-hostname `主机名`
# 端口号61616
提供JMS服务
# 8161
管理控制台服务
# 默认账号密码为 admin admin
# 修改密码
vim conf/jetty-realm.properties 
```





