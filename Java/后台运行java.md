```shell
# start.sh
# /bin/bash
# log.txt 
nohup java -jar /home/java/xxx.jar > log.txt &
```

```shell
sh start.sh
```

```bash
# 打包 跳过测试
mvn clean package -Dmaven.test.skip=true
# 远程调试
java -jar -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005 boot-tutu-mall-0.0.1-SNAPSHOT.java

```

