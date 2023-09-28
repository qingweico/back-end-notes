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
wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.5/rabbitmq-server-3.8.5-1.el7.noarch.rpm
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
## The default "guest" user is only permitted to access the server
## via a loopback interface (e.g. localhost).
# {loopback_users, []}
##
# loopback_users.guest = true

## Uncomment the following line if you want to allow access to the
## guest user from anywhere on the network.
loopback_users.guest = false
```

启用rabbitmq的管理平台插件

```bash
rabbitmq-plugins enable rabbitmq_management
```

```bash
# 开启rabbitmq server
service rabbitmq-server start
# 关闭rabbitmq服务
service rabbitmq-server stop
# 重启rabbitmq server
service rabbitmq-server restart
# 默认账号密码为guest
# 新增用户 admn admin的密码
rabbitmqctl add_user admin admin
# 修改密码
rabbitmqctl change_ password 用户名 新密码
# 删除用户
rabbitmqctl delete_user 用户名
# 查看用户清单
rabbitmqctl list_users -> 
user	tags
admin	[administrator]
guest	[administrator]
...
# 分配权限
rabbitmqctl set_user_tags admin administrator
# 新增 vhost
rabbitmqctl add_vhost[vhost_name]
# 删除 vhost
rabbitmqctl delete_vhost[vhost_name]
```

权限角色

- administrator : 可以登录控制台; 查看所有信息 并对rabbitmq进行管理 
- monToring : 监控者; 登录控制台 查看所有信息
- policymaker : 策略制定者; 登录控制台指定策略
- managment : 普通管理员; 登录控制

```bash
# 15672端口
```

### 延迟队列安装

rabbitmq中默认只有四中交换机类型：`headers、direct、fanout、topic`

```bash
# 安装一个x-delayed-message类型的交换机并放到/opt目录下
wget https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases/download/v3.8.0/rabbitmq_delayed_message_exchange-3.8.0.ez
```

```bash
# 将插件放到${rabbitmq_home}/plugins目录下
cp /opt/rabbitmq_delayed_message_exchange-3.8.0.ez /usr/lib/rabbitmq/lib/rabbitmq_server-3.8.5/plugins
```

```bash
# 启动插件
rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```

```bash
# 重启rabbitmq server
```

```bash
# 查看rabbitmq所有的插件列表
rabbitmq-plugins list
```

