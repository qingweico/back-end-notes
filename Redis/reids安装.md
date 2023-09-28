# 目录

[TOC]

## redis安装

下载redis [redis官网](http://www.redis.io)  /opt目录下解压

```bash
wget https://download.redis.io/releases/redis-6.0.5.tar.gz
tar -zxvf redis-6.0.5.tar.gz
```

进入redis-6.0.5目录中执行`make`命令进行编译, 然后再执行`make install` 命令安装(默认安装在/usr/local/bin目录下)

注意在使用`make`命令进行编译时可能会报错,gcc老版本不兼容redis6.0以上的版本(升级gcc即可) 查看gcc的版本 `gcc -v`

### 升级gcc

CentOS7默认gcc版本4.8.5

```bash
yum install centos-release-scl
```

```bash
yum install devtoolset-7 -y
```

设置devtoolset-7 为默认的gcc编译器

```bash
# 临时切换系统的gcc版本
scl enable devtoolset-7 bash
```

```bash
# 永久切换系统的gcc版本
echo "source /opt/rh/devtoolset-7/enable" >>/etc/profile
```

```bash
# 让配置生效
source /etc/profile
```

在redis-6.0.5目录下更改redis.conf配置文件,找到general下的deamonize,将`no`修改为`yes`(以后台方式运行),保存退出

```bash
# redis.conf
# redis密码
requirepass 123456
```

```bash
# 以配置文件方式启动
cd src
./redis-server ../redis.conf
```

```bash
./redis-cli -p 6379
ping
# (error) NOAUTH Authentication required.
# 密码认证即可
auth 123456
ping
PONG
```

## 图形化界面连接redis

### 修改redis.conf配置文件

```properties
# 注释掉redis与本地服务器的绑定关系
bind 127.0.0.1
```

```properties
# 关闭受保护模式
protected-mode no
```

### 开放3306端口

```bash
# 测试window是否可以连通Linux中redis端口
telnet 192.168.1.105 6379
```

```bash
# 若是云服务器设置安全组开放3306
firewall-cmd --zone=public --add-port=3306/tcp --permanent;
```

```bash
systemctl restart firewalld;
```

```bash
firewall-cmd --reload
```

### 重启redis

## windows redis 后台启动方法

```shell
# 注册windows服务
redis-server --service-install redis.windows.conf --loglevel verbose
# 启动
redis-server --service-start
# 停止
redis-server --service-stop
# 卸载
redis-server --service-uninstall
```

