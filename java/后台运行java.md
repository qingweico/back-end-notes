```shell
#start.sh
nohup java -jar /home/java/o2o-0.0.1-SNAPSHOT.war &
```

```shell
sh start.sh
```

```java
// 打包 跳过测试
mvn clean package -Dmaven.test.skip=true
```

