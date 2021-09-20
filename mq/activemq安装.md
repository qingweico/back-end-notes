## ActiveMQ

### 安装

```shell
# 下载activemq至/opt
wget https://archive.apache.org/dist/activemq/5.16.0/apache-activemq-5.16.0-bin.tar.gz
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
# 启动activemq
bin/activemq start
# 重新加载
bin/activemq reload
# 停止
bin/activemq stop
```





