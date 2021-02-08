##### 导入jar包：log4j-core-2.13.0.jar和log4j-api-2.13.0.jar

##### 在src目录下配置log4j2.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 对配置状态进行关闭，不把打印的细节在控制台中输出 -->
<!-- 该配置状态分为8个级别：高->低：OFF、FATAL、ERROR、WARN、INFO、DEBUG、TRACE、ALL -->
<Configuration status="OFF">
    <Appenders>
        <!-- 在控制台打印日志 -->
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} %-6level [%t] (%F:%L) - %msg%n"/>
        </Console>

        <!-- 日志输出分为6个级别：高->低：FATAL、ERROR、WARN、INFO、DEBUG、TRACE -->
        <!-- Debug调试级别 -->
        <!-- 这里根据你的个人习惯来写就好了，不细分的话写一个File就好了 -->
        <!-- 输出日志文件,位置自动保存在项目根目录下的logs文件夹中 -->
        <File name="MyDebugFile" fileName="logs/debug.log">
            <!-- 我这么写是为了把不同级别的日志筛选开来，便于查看 -->
            <!-- Filter过滤器，以此设置日志级别，进行不同程度的日志区分，如果不这样细分，可把Filter去掉 -->
            <!-- 比如这样设置：<ThresholdFilter level="debug" onMatch="ACCEPT" onMismatch="DENY"/>  -->
            <Filters>
                <!-- 第一步 onMatch="DENY"：匹配到info及更高级别就DENY过滤掉，其他的NEUTRAL保留 -->
                <ThresholdFilter level="info" onMatch="DENY" onMismatch="NEUTRAL"/>
                <!-- 第二步 onMatch="ACCEPT"：匹配到debug及更高级别就ACCEPT保留，其他的DENY过滤掉 -->
                <!-- 经过两步过滤，把debug以上的和以下的级别全部过滤，最终剩下debug级别 -->
                <ThresholdFilter level="debug" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <!-- 对打印语句的格式进行设置，这个太多放在后面讲 -->
            <PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} %-6level [%t] (%F:%L) - %msg%n"/>
        </File>

        <!-- Info信息级别 -->
        <File name="MyInfoFile" fileName="logs/info.log">
            <Filters>
                <ThresholdFilter level="warn" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} %-6level [%t] (%F:%L) - %msg%n"/>
        </File>

        <!-- Warn警告级别 -->
        <File name="MyWarnFile" fileName="logs/warn.log">
            <Filters>
                <ThresholdFilter level="error" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} %-6level [%t] (%F:%L) - %msg%n"/>
        </File>

        <!-- Error错误级别 -->
        <File name="MyErrorFile" fileName="logs/error.log">
            <Filters>
                <ThresholdFilter level="fatal" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} %-6level [%t] (%F:%L) - %msg%n"/>
        </File>

        <!-- Fatal严重错误级别 -->
        <File name="MyFatalFile" fileName="logs/fatal.log">
            <ThresholdFilter level="fatal" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss.SSS} %-6level [%t] (%F:%L) - %msg%n"/>
        </File>
    </Appenders>

    <!-- 定义Loggers，只有定义了Loggers并引入刚才的appender配置，配置才会生效 -->
    <Loggers>
        <Root level="trace">
            <!-- Only events at DIAG level or more specific are sent to the console. -->
            <AppenderRef ref="Console"/>
            <AppenderRef ref="MyDebugFile"/>
            <AppenderRef ref="MyInfoFile"/>
            <AppenderRef ref="MyWarnFile"/>
            <AppenderRef ref="MyErrorFile"/>
            <AppenderRef ref="MyFatalFile"/>
        </Root>
    </Loggers>
</Configuration>
```

#### 运行代码

```java
package com.java.servlet;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.core.Logger;
public class Log4j {
    //基于类的名称获取日志对象
    private static Logger logger = (Logger) LogManager.getLogger(Log4j.class);
     public static void main(String[] args)  throws InterruptedException{
         //进行不同级别的日志输出
       logger.trace("跟踪信息");
       logger.debug("调试信息");
       logger.info("输出信息");
        //为了便于前后输出日志的时间差
       Thread.sleep(3000);
       logger.warn("警告信息");
       logger.error("错误信息");
       logger.fatal("致命信息");
    }
}
```

