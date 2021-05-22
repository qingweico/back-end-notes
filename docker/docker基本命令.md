# 目录

[TOC]

## docker

docker:解决了运行环境和配置问题的软件容器,方便做持续继承并有助于整体发布的<font style="color:purple;font-size:20px">容器虚拟化技术</font>

```bash
# 查看Linux系统版本
cat /etc/redhat-release
//CentOS Linux release 7.8.2003 (Core)
```

```bash
# 查看Linux内核版本
uname -r
```

### docker的基本组成

- 容器(container)
- 镜像(image)
- 仓库(repository)

容器是镜像的一个实例,仓库是集中存放镜像的地方

### docker镜像基本命令

```java
docker --help //获取帮助命令
```

```java
docker images //查看本机的docker镜像
```

参数列表：

`-a `列出本地所有的镜像(含中间层的映射层)

`-q `只显示镜像id

`--digests`显示镜像的摘要信息

`--no-trunc`显示完整的镜像信息

```java
docker search 镜像名称 //查找所有的目标镜像
```

参数列表：

`-s  点赞数` 查找大于点赞数的所有目标镜像

```java
docker pull 镜像名称【:版本号】//下载镜像（默认拉取最新的镜像,可以在镜像后面跟着相应的版本号下载对应的版本）
```

```java
docker rmi  镜像名称【:版本号】 //删除目标镜像（默认删除最新镜像,可以在镜像后面跟着相应的版本号删除对应的版本）
```

`-f`强制删除

```java
docker rmi 镜像名称 镜像名称     //删除多个镜像
```

```java
docker rmi $(docker images -q) //删除所有的镜像
```

```java
docker commit -m="提交的描述信息" -a="作者" 容器ID 新的镜像名称:版本号 //提交容器使之成为一个新的镜像
```

### docker容器基本命令

```java
docker run [options] 镜像名称
```

参数列表：
`-i` 以交互模式运行容器,通常与`-t`一起使用

`-t` 为容器重新分配一个伪终端,通常于`-i`一起使用

`--name`自定义要运行容器的名称

```java
docker ps //列出当前正在运行的容器
```

参数列表：
`-l` 查看上次运行的容器

`-a` 查看所有已经运行过的容器信息

`-ql`查看上次运行的容器编号

`-n 3` 查看之前3次运行过的容器

```java
exit                           //直接结束容器的运行
ctrl + P + Q                   //离开容器并不结束后台运行
docker attach 容器ID            //重新进入容器
docker exec -t 容器ID 操作      //直接将在容器的执行结果返回宿主机
docker start 容器名称或者容器ID  //重启容器(后台方式运行容器)  
docker stop  容器名称或者容器ID  //停止容器   
docker kill  容器名称或者容器ID  //强制停止容器   
```

`attach和exec的区别` 前者并不会开启新的进程,而后者会开启新的进程

```java
[root@centos ~]# docker exec -t 08e5b25ce2a9 ls -l /tmp 
total 8
-rwx------. 1 root root 1379 Jun 11 02:35 ks-script-585nin8f
-rwx------. 1 root root  671 Jun 11 02:35 ks-script-z6zw_bhq
failed to resize tty, using default size
[root@centos ~]# 
```

```java
docker rm 容器名称或者容器ID     //删除历史运行过的容器记录
docker rm -f 容器名称或ID       //强制删除正在运行的容器
docker rm -f $(docker ps -q)   //强制删除所有正在运行的容器
```

```java
docker rm $(docker ps -aq)     //一次性删除多个历史容器记录
docker ps -qa | xargs docker rm 
```

```java
docker run -d 镜像名称          //以守护进程方式运行容器
```

```java
docker top 容器ID               //查看容器内运行的进程
```

```java
docker inspect 容器ID           //查看容器内部细节
```

```java
docker cp 镜像ID:容器文件路径 宿主机目标路径//将容器中的文件复制到宿主机中
```

```java
docker logs -ft tail 3 容器ID   //查看容器日志
```

`-f`:跟随最新的日志打印

`-t`:加入时间戳

`tail  数字`:显示最后多少条

### docke运行tomcat

```java
docker run -it -p 8848:8080 tomcat //以交互式运行tomcat 
```

`-p` 指定docker映射tomcat访问端口

`-P` 随机指定docker映射tomcat访问端口

```java
docker run -d -p 8848:8080 tomcat  //以守护进程的方式启动tomcat
```

