# 目录

[TOC]

## Centos7.x

### rpm安装

```bash
mysql-8.0.20-1.el7.x86_64.rpm-bundle.tar
```

### 检测本地是否有mariadb已存在的包

```bash
rpm -qa | grep mariadb
```

### 如果存在,则使用yum命令卸载

```bash
rpm -e --nodeps mariadb-libs-5.5.68-1.el7.x86_64
```

### 依次安装MySQL的组件

- **mysql-community-common**

```bash
rpm -ivh mysql-community-common-8.0.20-1.el7.x86_64.rpm
```

- **mysql-community-libs**

```bash
rpm -ivh mysql-community-libs-8.0.20-1.el7.x86_64.rpm
```

- **mysql-community-client**

```bash
rpm -ivh mysql-community-client-8.0.20-1.el7.x86_64.rpm
```

- **mysql-community-server**

```bash
#libaio.so.1()(64bit) is needed by MySQL-server
#yum install libaio
rpm -ivh mysql-community-server-8.0.20-1.el7.x86_64.rpm
```

### 初始化MySQL

```bash
mysqld --initialize
```

### 授权防火墙

```bash
chown mysql:mysql /var/lib/mysql -R
# 开启mysql服务
systemctl start mysqld.service
#设置开机自启mysql服务
systemctl enable mysqld
```

### 开启mysql服务

```bash
service mysqld start
```

### 查看数据库密码

```bash
cat /var/log/mysqld.log | grep password
```

### 登陆数据库(第一次登陆使用系统生成的密码,不便于记忆)

```bash
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

flush privileges;
```

```mysql
grant all privileges on *.* to 'root'@'%' with grant option;
```

```mysql
flush privileges;
```

### 开放3306端口

```bash
# 开放3306端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent;
```

```bash
# 重启防火墙
systemctl restart firewalld;
```

```bash
firewall-cmd --reload
```

### 设置时区

```mysql
set global time_zone = '+8:00';
```

### 安装JDK

```bash
vim /etc/profile
```

```shell
# java variable
JAVA_HOME=/usr/local/java/jdk-11.0.8
PATH=/usr/local/java/jdk-11.0.8/bin:$PATH
export JAVA_HOME PATH
```

```bash
# 让配置生效
source /etc/profile
```

### 安装Maven

```bash
# Maven 3.x 下载地址
# https://archive.apache.org/dist/maven/maven-3/
wget https://archive.apache.org/dist/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz -P /opt
# 解压
tar -zxvf /opt/apache-maven-3.6.3-bin.tar.gz -C /usr/local
# Maven Variable(/etc/profile)
MAVEN_HOME=/usr/local/apache-maven-3.6.3
export PATH=${MAVEN_HOME}/bin:${PATH}
```

