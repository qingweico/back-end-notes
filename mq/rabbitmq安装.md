# RabbitMQ

centos7.x

[查看erlang语言与rabbitmq之间的版本关系](https://www.rabbitmq.com/which-erlang.html)

[Install: RPM-based Linux](https://www.rabbitmq.com/install-rpm.html)

 [install a recent Erlang version via yum](https://packagecloud.io/rabbitmq/erlang/install#bash-rpm).

```bash
# 安装erlang前置脚本文件 通过yum
curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash
```

```bash
## 导入签名密钥
# 主RabbitMQ 签名密钥
rpm --import https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc
# 现代Erlang存储库
rpm --import https://packagecloud.io/rabbitmq/erlang/gpgkey
# RabbitMQ 服务器存储库
rpm --import https://packagecloud.io/rabbitmq/rabbitmq-server/gpgkey
```

```bash
# 安装erlang
yum install erlang
```

```bash
# 安装rabbitmq-server的前置准备
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
```

[rabbbitmq版本下载列表](https://github.com/rabbitmq/rabbitmq-server/tags)

```bash
# 下载rabbiitmq
```

```bash
# 安装rabbitmq
rpm -ivh rabbitmq-server-3.8.5-1.el7.noarch.rpm
## error: Failed dependencies: socat is needed by rabbitmq-server-3.8.5-1.el7.noarch
## yum -y install socat
```

配置文件

[rabbitmq.conf.example](https://github.com/rabbitmq/rabbitmq-server/blob/master/deps/rabbit/docs/rabbitmq.conf.example)

```bash
# 将文件重命名rabbitmq.conf放入/etc/rabbitmq/目录下
# 修改文件内容 保存退出
# 代表运行 guest 默认用户可以在非本地环境登录
 {loopback_users, [<<"guest">>]}, -----> {loopback_users, []}
```

启用rabbitmq的管理平台插件

```bash
rabbitmq-plugins enable rabbitmq_management
```

```bash
# 开启rabbitmq server
service rabbitmq-server start
# 重启rabbitmq server
service rabbitmq-server restart
```

