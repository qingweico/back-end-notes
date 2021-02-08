# 目录

[TOC]

## Centos7

### rpm安装

```mysql
mysql-8.0.20-1.el7.x86_64.rpm-bundle.tar
```

### 检测本地是否有mariadb已存在的包

```shell
rpm -qa | grep mariadb
```

### 如果存在，则使用yum命令卸载

```shell
rpm -e --nodeps mariadb-libs-5.5.56-2.el7.x86_64
```

### 依次安装MySQL的组件

- **mysql-community-common**

```shell
rpm -ivh mysql-community-common-8.0.20-1.el7.x86_64.rpm
```

- **mysql-community-libs**

```shell
rpm -ivh mysql-community-libs-8.0.20-1.el7.x86_64.rpm
```

- **mysql-community-client**

```shell
rpm -ivh mysql-community-client-8.0.20-1.el7.x86_64.rpm
```

- **mysql-community-server**

```shell
#libaio.so.1()(64bit) is needed by MySQL-server
#yum install libaio
rpm -ivh mysql-community-server-8.0.20-1.el7.x86_64.rpm
```

### 初始化MySQL

```shell
mysqld --initialize
```

### 授权防火墙

```shell
chown mysql:mysql /var/lib/mysql -R
#开启mysql服务
[root@mycentos ~] systemctl start mysqld.service
#设置开机自启mysql服务
[root@mycentos ~] systemctl enable mysqld
```

### 查看数据库密码

```shell
cat /var/log/mysqld.log | grep password  ccth!RNF.9VE
```

### 登陆数据库（第一次登陆使用系统生成的密码，不便于记忆）

```mysql
mysql -u root -p;
```

### 修改密码

```mysql
alter user 'root'@'localhost' identified with mysql_native_password by 新密码;     #密码一定要加加引号
```

### 查询远程访问

```mysql
 mysql>  use mysql;
 mysql>  select host,user from user ;
+-----------+------------------+
| host      | user             |
+-----------+------------------+
| localhost | mysql.infoschema |
| localhost | mysql.session    |
| localhost | mysql.sys        |
| localhost | root             |
| localhost | test             |
+-----------+------------------+
```

### 开启远程访问

```mysql
/*二种方法都可以*/
update user set host = "%" where user = "root";
create user 'root'@'%' identified with mysql_native_password by "远程登陆密码";         #密码一定要加引号
```

```mysql
grant all privileges on *.* to 'root'@'%' with grant option;
```

```mysql
flush privileges;
```

### 开放3306端口

```shell
#开放3306端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent;
```

```shell
#重启防火墙
systemctl restart firewalld;
```

```shell
firewall-cmd --reload
```

### 设置时区

```mysql
set global time_zone = '+8:00';
```

### 安装jdk

```shell
vim /etc/profile
```

```shell
#配置环境变量
JAVA_HOME=/use/local/java/jdk-11.0.8
PATH=/use/local/java/jdk-11.0.8/bin:$PATH
export JAVA_HOME PATH
```

```shell
#让配置生效
source /etc/profile
```





