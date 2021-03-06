# 目录

[TOC]

## 容器数据卷

### 容器数据卷的作用

- 容器的持久化
- 容器的继承+容器之间共享数据

### 容器的添加

#### 直接命令添加

```java
docker run -it -v 宿主机绝对路径目录:容器内目录 镜像名
```

容器和宿主机之间可以进行数据共享

当容器停止运行并且退出后,主机修改数据后,当容器重启时数据仍然同步

```java
docker run -it -v 宿主机绝对路径目录:容器内目录:ro 镜像名 //容器只可读数据不可写数据
```

#### DockerFile添加

构建dockerFile文件

```java
From centos
VOLUME ["/container1","/container2"]
CMD echo "finished ------------------------->success!"
CMD /bin/bash
```

build生成新的镜像

```java
docker build -f /docker/dockerFile -t mycentos .
```

`-f` file 指明要构建的dockerFile文件 如果当目录下存在文件名为DockerFile的文件则可以不用指定dockerFile文件 `-f`也可以省略

`-t` 生成新的镜像名称空间

`.`代表当前目录

```java
[root@centos docker]# docker build -f /docker/dockerFile -t mycentos .
Sending build context to Docker daemon  2.048kB
Step 1/4 : From centos
 ---> 831691599b88
Step 2/4 : VOLUME ["/container1","/container2"]
 ---> Running in 83c9cff48a34
Removing intermediate container 83c9cff48a34
 ---> 2f1d4c79d1b0
Step 3/4 : CMD echo "finished ------------------------->success!"
 ---> Running in 6ad88f03aed5
Removing intermediate container 6ad88f03aed5
 ---> 160e2c425e4c
Step 4/4 : CMD /bin/bash
 ---> Running in 120d61ae0150
Removing intermediate container 120d61ae0150
 ---> 93872055e998
Successfully built 93872055e998
Successfully tagged mycentos:lates
```

```java
"Mounts": [
    {
        "Type": "volume",
        "Name": "7dba20d30f6fe342a199bb94ea8f2eeee35571f2e808895428ccfce045ce107a",
        "Source":  #默认宿主机地址   与container1数据同步
            "/var/lib/docker/volumes/7dba20d30f6fe342a199bb94ea8f2eeee35571f2e808895428ccfce045ce107a/_data",
        "Destination": "/container1",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    },
    {
        "Type": "volume", 
        "Name": "6949ba7c7ec80acd15f4aea79e3823d61b89a823b9769f9512f41866b360754e",
        "Source":  #默认宿主机地址   与container2数据同步           "/var/lib/docker/volumes/6949ba7c7ec80acd15f4aea79e3823d61b89a823b9769f9512f41866b360754e/_data",
        "Destination": "/container2",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
],
```

### 数据卷容器

挂载容器卷的容器  而其他容器可以通过挂载这个(父容器)实现数据共享

作用：实现容器间的传递共享

```java
docker run -it --name c1  mycentos                  //生成容器c1
```

```java
docker run -it --name c2 --volumes-from c1 mycentos //生成容器c2并继承容器c1
```

```java
docker run -it --name c3 --volumes-from c1 mycentos //生成容器c3并继承容器c1
```

三个容器中拥有相同的两个数据卷container1和container2,此时在该数据卷中所做的任何读写操作都能在容器之间中同步实现

删除c1容器后,c2和c3之间仍然可以实现数据共享

接着删除c2容器后,c3仍然可以访问原来的数据

即容器之间配置信息的传递,数据卷的生命周期直到没有容器使用为止