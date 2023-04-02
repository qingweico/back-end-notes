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

- mvn clean

  用于清除之前构建生成的所有文件; 其中具体为清除了target目录中的所有文件,包括该目录(删除了install生成的所有文件)

- validate

  用于验证项目是否正确,并且其中有必要信息是否都可用

- mvn compile

  编译项目的源代码, 主要是java文件; 一般是编译scr/main/java或是scr/test/java里面的文件

- mvn test

  测试compile中编译出来的代码

- mvn package

  生成target目录, 编译、测试代码, 生成jar/war文件 

  获取compile中编译好的代码并将其打包成可分类的格式(jar包或者 war包)

- vertify

  用来验证test;检查test的结果是否满足标准

- mvn install

  安装至本地仓库;即在本地repository中安装jar,供其他项目使用

- mvn site

  生成项目的站点文档

- deploy

  复制最终的包至远程仓库,共享给其它开发人员和项目

## java依赖的范围（默认的都是compile）

### test范围依赖 （比如junit）

- 对主程序是否有效：无效
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

## Maven程序的目录结构(Web项目)

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

## Maven 设置多profiles

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

	<localRepository>C:\Java\repository</localRepository>
    
	<pluginGroups>
        
	</pluginGroups>

	<proxies>

	</proxies>


	<servers>

	</servers>

	<mirrors>
		
	</mirrors>

	<profiles>
		<profile>
			<id>other</id>
			<repositories>
				<repository>
					<id>other</id>
					<url>xxx</url>
					<releases>
						<enabled>true</enabled>
					</releases>
					<snapshots>
						<enabled>true</enabled>
						<updatePolicy>always</updatePolicy>
					</snapshots>
				</repository>
			</repositories>
		</profile>
		<profile>
			<id>ali-yun</id>
			<repositories>
				<repository>
					<id>ali-yun</id>
					<url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
					<releases>
						<enabled>true</enabled>
					</releases>
					<snapshots>
						<enabled>true</enabled>
						<updatePolicy>always</updatePolicy>
					</snapshots>
				</repository>
			</repositories>
		</profile>
	</profiles>
	<activeProfiles>
		<activeProfile>other</activeProfile>
		<activeProfile>ali-yun</activeProfile>
	</activeProfiles>
</settings>
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

### 其他

```shell
# 查看 Maven 依赖树
mvn dependency:tree
```

