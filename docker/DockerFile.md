# 目录

[TOC]

## DockerFile

### dockerFile

dockerFile是用来构建docker镜像的构建文件,是由一系列命令或者参数构成的脚本

### docker语法规则

- 每条保留字指令都必须是大写字母且后面至少要跟一个参数
- 指令按照从上到下顺序执行
- `#`表示注释
- 每条指令都会创建一个新的镜像层,并对镜像层提交

### docker体系结构

#### 保留关键字

- FROM                基础镜像  当前镜像基于哪个镜像构建
- MAINTAINER          镜像维护者
- RUN                 容器构建时需要运行的命令
- EXPOSE              当前容器对外暴露的端口
- WORKDIR             创建容器后终端默认的登陆位置
- ENV                 用来构建镜像中设置环境变量
- ADD                 将宿主机目录下的文件复制到镜像中并可以自动解压tar包和处理URL
- COPY                复制文件或目录到镜像中
- COLUME              容器数据卷用来数据保存和持久化工作
- CMD                 指定一个容器启动时要运行的命令且DockerFile中可以有多个CMD命令,但只有最后一个生效；CMD命令会被docker  run后面的命令替换

```java
CMD ["catalina.sh", "run"]
```

```java
//ls -l命令会覆盖tomcat dockerFile文件中["catalina.sh", "run"]命令 即不会运行tomcat容器只会进入到容器中执行ls -l命令
docker run -it tomcat ls -l 
```

- ENTRYPOINT     和CMD作用样但是会在原来的命令上追加命令,即所有的命令会一起起作用
- ONBUILD        被继承镜像的(父镜像)dockerFile会在子镜像运行时被触发一系列命令

dockerFile_father

```java
from centos
ONBUILD RUN echo "father image is onbuild......"
```

```java
docker build -f /docker/dockerFile_father -t mycentos_father .  //构建父镜像
```

dockerFile_son

```java
from mycentos_father
CMD echo "son image is build....."
```

```java
docker build -f /docker/dockerFile_son -t mycentos_son .         //构建子镜像
```

```java
Sending build context to Docker daemon  4.096kB
Step 1/2 : from mycentos_father
# Executing 1 build trigger                                       //执行了一个触发器
 ---> Running in f77cce98c31d
father image is onbuild......
Removing intermediate container f77cce98c31d
 ---> 89c82a0b21ed
Step 2/2 : CMD echo "son iamge is build....."
 ---> Running in 74dd8aa75018
Removing intermediate container 74dd8aa75018
 ---> 26a3187c5378
Successfully built 26a3187c5378
Successfully tagged mycentos_son:latest
```

### 自定义DockerFile文件

```
from centos
ENV myPath /home
WORKDIR $myPath
RUN yum -y install vim
RUN yum -y install net-tools
EXPOSE 80
CMD echo "->10%"
CMD echo "-->20%"
CMD echo "--->30%"
CMD echo "---->40%"
CMD echo "----->50%"
CMD echo "------>60%"
CMD echo "------->70%"
CMD echo "-------->80%"
CMD echo "--------->90%"
CMD echo "---------->100%"
CMD echo "----------->finished!"
CMD echo "======================="
CMD /bin/bash
```

```java
docker build -f /docker/dockerFile -t mycentos. //自定义构建镜像文件
```

























