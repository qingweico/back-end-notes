# 目录

[TOC]

## docker安装mysql

```shell
docker pull mysql                #拉取最新版的mysql镜像
```

```shell
mkdir -p /docker/docker_mysql    #在根目录下创建docker目录,docker_mysql目录
cd /docker/docker_mysql          #进入目录
```

### 运行mysql

```shell
docker run --name MYSQL   
    -v $PWD/conf:/etc/mysql/my.cnf   #$PWD代表当前路径
    -v $PWD/logs:/logs               # -v 挂载
    -v $PWD/data:/var/lib/mysql 
    -e MYSQL_ROOT_PASSWORD=123456    #初始化msql密码
    -d -i -p 3307:3306 mysql         #后台启动 linux下3307端口映射docker容器中mysql默认的3306端口
```

```shell
docker exec -it mysql容器ID或名称 /bin/bash    #进入mysql容器中
```

```shell
mysql -u root -p 123456                      #登陆mysql
```

### 开启远程访问

```mysql
alter user 'root'@'%' identified with mysql_native_password by "远程登陆密码";       #密码一定要加引号
```

```mysql
grant all privileges on *.* to 'root'@'%' with grant option;                       #打开root的远程访问权限
```

```mysql
flush privileges;                                                                  #刷新权限
```

```mysql
docker logs -f --tail 10 容器ID                                                     #查看日志
```

## docker安装redis

```shell
docker pull redis                   #拉取最新版本的redis镜像
```

```shell
mkdir -p /docker/docker_redis 
cd /docker/docker_redis
```

```shell
mkdir $PWD/conf
cd conf
vim redis.conf                      #在宿主机上创建redis配置文件
```

```shell
docker run -d --name redis -p 6379:6379
    -v /docker/docker_redis/conf/redis.conf:/redis.conf  #同步主机的redis配置文件
    -v /docker/docker_redis/data:/data redis
    redis-server --appendonly yes                        #开启aof持久化
```
