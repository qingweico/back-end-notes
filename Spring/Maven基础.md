# 目录

[TOC]

## Maven的核心概念(Apache Maven 3.6.3)

- 约定的目录结构
- POM
- 坐标
- ***依赖***
- 仓库
- 生命周期/插件/目标
- 继承
- 聚合

pom:Project Object Model  项目对象模型

## Maven工程坐标与仓库中路径的关系关系

```xml
<groudId>org.springframework</groudId>
<artifactId>spring-core</artifactId>
<version>4.0.0.RELEASE</version>
```

对应于: ***org/spring-framework/spring-core/4.0.0.RELEASE/spring-core-4.0.0.RELEASE.jar***

## 常用的Maven命令

- mvn clean：清理
- mvn compile：编译主程序
- mvn test-compile：编译测试程序
- mvn test：执行测试程序
- mvn package：打包
- mvn install：安装
- mvn site：生成站点

## java依赖的范围（默认的都是compile）

### test范围依赖 （比如junit）

- 对主程序是否有效 ：无效
- 对测试程序是否有效：有效
- 是否参与打包：不参与
- 是否参与部署：不参与

--------------

### compile依赖范围

- 对主程序是否有效： 有效
- 对测试程序是否有效：有效
- 是否参与打包：参与
- 是否参与部署： 参与

--------

### provide依赖范围（比如servlet-api jsp-api）

- 对主程序是否有效：有效
- 对测试是否有效 ： 有效
- 是否参与打包：不参与
- 是否参与部署： 不参与

-----

runtime依赖的范围

- 对于主程序是否有效： 无效
- 对于测试是否有效：有效
- 对于运行是否有效：有效

## Maven程序的目录结构（Web项目）

----

## 在IDEA中构建Maven项目

- 首先配置好Maven在本地的环境变量 path和MAVEN_HOME

```properties
MAVEN_HOME: E:\maven\apache-maven-3.6.3
Path: E:\maven\apache-maven-3.6.3\bin
```

- 在Maven根目录下conf文件下找到settings设置本地仓库,默认是在用户目录下.m2文件夹下

  ```xml
  <localRepository>E:\maven\repository</localRepository>
  ```

- 设置中央仓库镜像

  ```xml
  <mirrors>
      <mirror>
          <id>alimaven</id>
          <mirrorOf>central</mirrorOf>
          <name>aliyun maven</name>
          <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
      </mirror>
  </mirrors>
  ```
  
- 运行一个Web项目时servlet和jsp的范围要设置为provided才OK

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.0</version>
    <scope>provided</scope>
</dependency>
```

- maven settings.xml全局配置jdk环境

   ```xml
   <profiles>
     	<profile>
         <id>jdk-1.8</id>
         <activation>
           <activeByDefault>true</activeByDefault>
           <jdk>1.8</jdk>
         </activation>
         <properties>
           <maven.compiler.source>1.8</maven.compiler.source>
           <maven.compiler.target>1.8</maven.compiler.target>
           <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
         </properties>
     </profile>
     </profiles>
   ```
   
- Maven内置的Tomcat是tomcat6,如果JDK版本过低的话会出现运行不兼容的情况而报错

  - 在pom.xml中引入tomcat7或以上版本的依赖
  - 或者在pom.xml中引入jdk8及其以上的依赖

```xml
<!--IDEA中设置maven工程下LanguageLevel运行级别-->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
    </configuration>
</plugin>
```

## 关于Maven工程下c3p0报错问题

- 首先在pom.xml文件中引入c3p0依赖和辅助包依赖

   ```xml
   <dependency>
        <groupId>com.mchange</groupId>
        <artifactId>c3p0</artifactId>
        <version>0.9.5.2</version>
      </dependency>
      <dependency>
        <groupId>com.mchange</groupId>
        <artifactId>mchange-commons-java</artifactId>
        <version>0.2.15</version>
   </dependency>
   ```

- 其次一定要把c3p0-config.xml文件放在src的根目录下

- 最后最重要的一点是一定要把src目录设置为Source root 因为Maven工程在创建后的src文件夹并不会被默认为java源文件,所以需要手动设置,不然会因为找不到src源文件下的c3p0-config.xml文件而报错