[TOC]

## 第十七章 Spring 事件

### Java 事件/监听器编程模型

设计模式 - 观察者模式扩展

- 可观者对象(消息发送者) - java.util.Observable
- 观察者 - java.util.Observer

标准化接口

- 事件对象 - java.util.EventObject
- 事件监听器 - java.util.EventListener

### 面向接口的事件/监听器设计模式

事件/监听器场景举例

| Java 技术规范   | 事件接口(extends EventObject)         | 监听器接口(extends EventListener)        |
| :-------------- | ------------------------------------- | ---------------------------------------- |
| JavaBeans       | java.beans.PropertyChangeEvent        | java.beans.PropertyChangeListener        |
| Java AWT        | java.awt.event.MouseEvent             | java.awt.event.MouseListener             |
| Java Swing      | javax.swing.event.MenuEvent           | javax.swing.event.MenuListener           |
| Java Preference | java.util.prefs.PreferenceChangeEvent | java.util.prefs.PreferenceChangeListener |

在 Spring 里面只有单事件监听, 没有多事件监听(在面向接口的情况下)

### 面向注解的事件/监听器设计模式

 事件监听器注解场景举例

| Java 技术规范 | 事件注解                        | 监听器注解                            |
| ------------- | ------------------------------- | ------------------------------------- |
| Servlet 3.0+  |                                 | @javax.servlet.annotation.WebListener |
| JPA 1.0+      | @javax.persistence.PostPersist  |                                       |
| Java Common   | @javax.annotation.PostConstruct |                                       |
| EJB 3.0+      | @javax.ejb.PrePassivate         |                                       |
| JSF 2.0+      | @javax.faces.event.ListenerFor  |                                       |

### Spring 标准事件 - ApplicationEvent

Java 标准事件 java.util.EventObject 的扩展

- 扩展特性: 事件发生时间戳

Spring 应用上下文 ApplicationEvent 扩展 - ApplicationContextEvent

- Spring 应用上下文 (ApplicationContext) 作为事件源

- 具体实现

  >org.springframework.context.event.ContextClosedEvent
  >
  >org.springframework.context.event.ContextRefreshedEvent
  >
  >org.springframework.context.event.ContextStartedEvent
  >
  >org.springframework.context.event.ContextStoppedEvent

### 基于接口的 Spring 事件监听器

Java 标准事件监听器 java.util.EventListener 扩展

- 扩展接口 - org.springframework.context.ApplicationListener
- 设计特点: 单一类型事件处理(并不是指它只处理某一种类型, 而是指它一次性只处理一个类型的事件)
- 处理方法: onApplicationEvent(ApplicationEvent event)
- 事件类型: org.springframework.context.ApplicationEvent

### 基于注解的 Spring 事件监听器

Spring 注解 - org.springframework.context.event.EventListener

| 特性                 | 说明                                      |
| -------------------- | ----------------------------------------- |
| 设计特点             | 支持多 ApplicationEvent 类型;无需接口约束 |
| 注解目标             | 方法                                      |
| 是否支持异步执行     | 支持                                      |
| 是否支持泛型类型事件 | 支持                                      |
| 是否支持顺序控制     | 支持;配合 @Order 注解控制                 |

### 注册 Spring AppicationListener

- 方法一: ApplicationListener作为 Spring Bean 注册

- 方法二: 通过 ConfigurableApplicationContext API 注册

### Spring 事件发布器

方法一: 通过 org.springframework.context.ApplicationEventPublisher 发布 Spring 事件

>获取 ApplicationEventPublisher(可以通过依赖注入)

方法二: 通过org.springframework.context.event.ApplicationEventMulticaster 发布 Spring 事件

> 获取ApplicationEventMulticaster(可以通过依赖注入也可以通过依赖查找)

### Spring 层次性上下文事件传播

当 Spring 应用出现多层次 Spring 应用上下文 (ApplicationContext ) 时, 由子 ApplicationContext 发起 Spring 事件可能会传递到其 Parent ApplicationContext (直到 Root) 的过程

如何避免?

定位 Spring 事件源 (ApplicationContext ) 进行过滤处理

### Spring 内建事件

ApplicationContextEvent 派生事件

- ContextRefreshedEvent - Spring 应用上下文就绪事件
- ContextStartedEvent - Spring 应用上下文启动事件
- ContextStoppedEvent - Spring 应用上下文停止事件
- ContextClosedEvent \- Spring 应用上下文关闭事件

### Spring 4.2 Payload 事件

Spring Payload 事件 - org.springframework.context.PayloadApplicationEvent

使用场景: 简化 Spring 事件发送, 关注事件源主题

发送方法: ApplicationEventPublisher#publishEvent(Object event)

### 自定义 Spring 事件

>扩展 org.springframework.context.ApplicationEvent
>
>实现 org.springframework.context.ApplicationListener
>
>注册 org.springframework.context.ApplicationListener

### 依赖注入 ApplicationEventPublisher

直接的方式

- 通过 ApplicationEventPublisherAware 回调接口
- 通过 @Autowired ApplicationEventPublisher

间接的方式

ConfigurableApplicationContext 扩展了 ApplicationEventPublisher

### 依赖查找 ApplicationEventMulticaster 

查找条件

- Bean 名称 - applicationEventMulticaster
- Bean 类型 - org.springframework.context.event.ApplicationEventMulticaster

### ApplicationEventPublisher 底层实现

- 接口 - org.springframework.context.event.ApplicationEventMulticaster
- 抽象类 - org.springframework.context.event.AbstractApplicationEventMulticaster
- 实现类 - org.springframework.context.event.SimpleApplicationEventMulticaster

ApplicationEventPublisher 和 ApplicationEventMulticaster之间通过 AbstractApplicationContext 做桥接

### 同步和异步 Spring 事件广播

基于实现类 - org.springframework.context.event.SimpleApplicationEventMulticaster

- 模式切换 - setTaskExecutor(java.util.concurrent.Executor)

  > 默认模式: 同步
  >
  > 异步模式: 如java.util.concurrent.ThreadPoolExecutor

- 设计缺陷: 非基于接口契约编程

基于注解 - @org.springframework.context.event.EventListener

- 模式切换

  > 默认模式: 同步
  >
  > 异步模式: 标注@org.springframework.scheduling.annotation.Async

- 设计限制: 无法直接实现同步/异步动态切换

### Spring 4.1 事件异常处理

Spring 3.0 错误处理接口 - org.springframework.util.ErrorHandler

使用场景

- Spring 事件(Events)

  > org.springframework.context.event.SimpleApplicationEventMulticaster Spring 4.1 开始支持

- Spring 本地调度(Scheduling)

  > org.springframework.scheduling.concurrent.ConcurrentTaskScheduler
  >
  > org.springframework.scheduling.concurrent.ThreadPoolTaskScheduler

###  Spring 事件/监听器实现原理

核心类 - org.springframework.context.event.SimpleApplicationEventMulticaster

- 设计模式: 观察者模式扩展

  >被观察者 - org.springframework.context.ApplicationListener
  >
  >- API 添加
  >- 依赖查找
  >
  >通知对象 - org.springframework.context.ApplicationEvent

- 执行模式: 同步异步

- 异常处理: org.springframework.util.ErrorHandler

- 泛型处理: org.springframework.core.ResolvableType

### 课外资料

Spring Boot 事件

| 事件类型                            | 发生时机                                |
| ----------------------------------- | --------------------------------------- |
| ApplicationStartingEvent            | 当 Spring Boot 应用已启动时             |
| ApplicationStartedEvent             | 当 Spring Boot 应用已启动时             |
| ApplicationEnvironmentPreparedEvent | 当 Spring Boot Environment 实例已准备时 |
| ApplicationPreparedEvent            | 当 Spring Boot 应用预备时               |
| ApplicationReadyEvent               | 当 Spring Boot 应用完全可用时           |
| ApplicationFailedEvent              | 当 Spring Boot 应用启动失败时           |

Spring Cloud 事件

| 事件类型                   | 发生时机                              |
| -------------------------- | ------------------------------------- |
| EnvironmentChangeEvent     | 当 Environment 示例配置属性发生变化时 |
| HeartbeatEvent             | 当 DiscoveryClient 客户端发送心跳时   |
| InstancePreRegisteredEvent | 当服务实例注册前                      |
| InstanceRegisteredEvent    | 当服务实例注册后                      |
| RefreshEvent               | 当 RefreshEndpoint 被调用后           |
| RefreshScopeRefreshedEvent | 当 Refresh Scope Bean 刷新后          |

在 Spring Boot 和 Spring Cloud 中 对 Spring 事件监听机制的原理和底层都是一样的

### 面试题

#### Spring 事件核心接口/组件

- Spring 事件 - org.springframework.context.ApplicationEvent
- Spring 事件监听器 - org.springframework.context.ApplicationListener
- Spring 事件发布器 - org.springframework.context.ApplicationEventPublisher
- Spring 事件广播器 - org.springframework.context.event.ApplicationEventMulticaster

#### Spring 同步和异步事件处理的使用场景

Spring 同步事件 - 绝大多数 Spring 使用场景 如 ContextRefreshedEvent

Spring 异步事件 - 主要 @EventListener 与 @Async 配置, 实现异步处理, 不阻塞主线程(指事件发送的线程和事件处理的线程不是同一个线程)

## 第十八章 Spring 注解

### Spring 核心注解场景分类

Spring 模式注解

| Spring 注解    | 场景说明           | 起始版本 |
| -------------- | ------------------ | -------- |
| @Repository    | 数据仓储模式注解   | 2.0      |
| @Component     | 通用组件模式注解   | 2.5      |
| @Service       | 服务模式注解       | 2.5      |
| @Controller    | Web 控制器模式注解 | 2.5      |
| @Configuration | 配置类模式注解     | 3.0      |

<sub>*这里的Controller是指Spring MVC场景; MVC最早来自桌面软件</sub>

装配注解

| Spring 注解     | 场景说明                                    | 起始版本 |
| --------------- | ------------------------------------------- | -------- |
| @ImportResource | 替换 XML 元素 <import>                      | 2.5      |
| @Import         | 导入 Configuration 类                       | 2.5      |
| @ComponentScan  | 扫描指定 package 下标注 Spring 模式注解的类 | 3.1      |

依赖注入注解

| Spring 注解 | 场景说明                           | 起始版本 |
| ----------- | ---------------------------------- | -------- |
| @Autowired  | Bean 依赖注入;支持多种依赖查找方式 | 2.5      |
| @Qualifier  | 细粒度的 @Autowired 依赖查找       | 2.5      |

### Spring 注解编程模型

- 元注解(标注在注解上的注解)
- Spring 模式注解
- Spring 组合注解
- Spring 注解属性别名和覆盖

### Spring 元注解 (Meta-Annotations)

举例说明

- java.lang.annotation.Documented
- java.lang.annotation.Inherited
- java.lang.annotation.Repeatable

### Spring 模式注解 (Stereotype Annotations)

@Component 的派生性

元标注 @Component 的注解在 XML 元素 `<context:component-scan>`或注解 @ComponentScan 扫描中派生了 @Component 的特性, 并且从 Spring Framer work 4.0 开始支持多层次派生性

@Component 派生的注解有

- @Repository
- @Service
- @Controller
- @Configuration
- @SpringBootConfiguration

@Component 派生性原理

核心组件 - org.springframework.context.annotation.ClassPathBeanDefinitionScanner

- 父类 - org.springframework.context.annotation.ClassPathScanningCandidateComponentProvider

资源处理 - org.springframework.core.io.support.ResourcePatternResolver

资源转换成类元信息 - org.springframework.core.type.classreading.MetadataReaderFactory

类元信息 - org.springframework.core.type.ClassMetadata

- ASM 实现 - org.springframework.core.type.classreading.ClassMetadataReadingVisitor
- 反射实现 - org.springframework.core.type.StandardClassMetadata 

注解元信息 - org.springframework.core.type.AnnotationMetadata

- ASM 实现 - org.springframework.core.type.classreading.AnnotationMetadataReadingVisitor
- 反射实现 - org.springframework.core.type.StandardAnnotationMetadata

@ComponentScan 处理过程(通过 ASM 扫描; 可以绕过类加载过程中的字节码的校验机制; 在 Spring 5.0引入了@indexed注解)

ConfigurationClassParser#doProcessConfigurationClass(ConfigurationClass configClass, SourceClass sourceClass)

ComponentScanAnnotationParser#parse(AnnotationAttributes componentScan, final String declaringClass)

ClassPathBeanDefinitionScanner#doScan(String... basePackages)

ClassPathScanningCandidateComponentProvider#findCandidateComponents(String basePackage)

ClassPathScanningCandidateComponentProvider#scanCandidateComponents(String basePackage)

ClassPathScanningCandidateComponentProvider#isCandidateComponent(MetadataReader metadataReader)

ClassPathScanningCandidateComponentProvider(boolean useDefaultFilters)

ClassPathScanningCandidateComponentProvider#registerDefaultFilters()

### Spring 组合注解(Composed Annotations)

基本定义: Spring 组合注解中的元素允许是 Spring 模式注解与其他 Spring 功能性注解的任意组合

实现: org.springframework.core.annotation.AnnotationAttributes

### Spring @Enable 模块驱动

@Enable 模块驱动是以 @Enable 为 前缀的注解驱动编程模型

模块是指具备相同领域的功能组件集合 组合所形成一个独立的单元

比如

- Web MVC 模块
- AspectJ 代理模块
- Caching 模块
- JMX 模块 (Java 管理扩展)
- Async 模块

注解

- @EnableWebMvc
- @EnableTransactionManagement
- @EnableCaching
- @EnableMBeanExport
- @EnableAsync

### Spring 条件注解

基于配置条件注解 - @org.springframework.context.annotation.Profile

- 关联对象 - org.springframework.core.env.Environment 中的 Profiles
- 实现变化 从 Spring 4.0 开始 @Profile 基于 @Conditional 实现

基于编程条件注解 - @org.springframework.context.annotation.Conditional

- 关联对象 - org.springframework.context.annotation.Condition 接口 具体实现

@Conditional 实现原理

- 上下文对象 - org.springframework.context.annotation.ConditionContext  接口
- 条件判断  - org.springframework.context.annotation.ConditionEvaluator 包级私有
- 配置阶段 - org.springframework.context.annotation.ConfigurationCondition.ConfigurationPhase
- 判断入口 - org.springframework.context.annotation.ConfigurationClassPostProcessor
  - org.springframework.context.annotation.ConfigurationClassParser 包级私有

Profile 即 通过一种配置的方式来隔离某一种环境

### 课外资料

Spring Boot 注解

| Spring Boot 注解         | 场景说明                 | 起始版本 |
| ------------------------ | ------------------------ | -------- |
| @SpringBootConfiguration | SpringBoot 配置类        | 1.4.0    |
| @SpringBootApplication   | SpringBoot 应用引导注解  | 1.2.0    |
| @EnableAutoConfiguration | SpringBoot  激活自动装配 | 1.0.0    |

Spring Cloud 注解

| 注解                    | 场景说明                            | 版本  |
| ----------------------- | ----------------------------------- | ----- |
| @SpringCloudApplication | Spring Cloud 应用引导注解           | 1.0.0 |
| @EnableDiscoveryClient  | Spring Cloud 激活服务发现客户端注解 | 1.0.0 |
| @EnableCircuitBreaker   | Spring Cloud 激活熔断注解           | 1.0.0 |

### 面试题

#### @EventListener 的工作原理

核心 API - org.springframework.context.event.EventListenerMethodProcessor

## 第十九章 Spring Environment  抽象

### 理解 Spring Environment  抽象

- 统一的 Spring 配置属性管理

  Spring Framework 3.1 开始引入 Environment  抽象 它统一 Spring 配置属性的储存 包括占位符处理和类型转换 不仅完整地替换 PropertyPlaceholderConfigure 而且还支持更丰富的配置属性源(PropertySource)

- 条件化 Spring Bean 装配管理

  通过 Environment  Profiles 信息 帮助 Spring 容器提供条件化地装配 Bean

### Spring Environment 接口使用场景

- 用于属性占位符处理
- 用于转换 Spring 配置属性类型
- 用于储存 Spring 配置属性源(Property)
- 用于 Profile 状态的维护

### Environment  占位符处理

### 处理条件配置 Spring Profiles

Spring 3.1 条件配置

API

- org.springframework.core.env.ConfigurableEnvironment
  - 修改 [setActiveProfiles] [setDefaultProfiles] [addActiveProfile]
  - 获取 [getActiveProfiles] [getDefaultProfiles]
  - 匹配 acceptsProfiles(String... profiles) acceptsProfiles(Profiles profiles)

注解: @org.springframework.core.env.Profiles

Spring 4 重构 @Profile

### 依赖注入 Environment  

直接依赖注入

- 通过 EnvironmentAware接口
- 通过 @Autowired 注入 Environment

间接依赖注入

- 通过 ApplicationContextAware 接口回调
- 通过 @Autowired 注入 ApplicationContext

### 依赖查找 Environment 

直接依赖查找

- org.springframework.context.ConfigurableApplicationContext#ENVIRONMENT_BEAN_NAME

间接依赖查找

- org.springframework.context.ConfigurableApplicationContext#getEnvironment

### 依赖注入 @Value

- AutowiredAnnotationBeanPostProcessor

- DefaultListableBeanFactory#doResolveDependency

- QualifierAnnotationAutowireCandidateResolver#getSuggestedValue

### Spring 类型转换在 Environment  中的运用

Environment  底层的实现

底层实现  - org.springframework.core.env.PropertySourcesPropertyResolver

- 核心方法 - convertValueIfNecessary(Object value, @Nullable Class<T> targetType)

底层服务 - org.springframework.core.convert.ConversionService

- 默认的实现 - org.springframework.core.convert.support.DefaultConversionService

### Spring 类型转换在 @Value 中的使用

@Value 底层实现

底层实现 - AutowiredAnnotationBeanPostProcessor

- DefaultListableBeanFactory#doResolveDependency

底层服务 - org.springframework.beans.TypeConverter

- org.springframework.beans.SimpleTypeConverter 委派给 TypeConverterDelegate

- 默认实现 - org.springframework.beans.TypeConverterDelegate
  - java.beans.PropertyEditor
  - org.springframework.core.convert.ConversionService

### Spring 配置属性源 Property

API 

- 单配置属性源 - org.springframework.core.env.PropertySource
- 多配置属性源 - org.springframework.core.env.PropertySources

注解

- 单配置属性源 - @org.springframework.context.annotation.PropertySource
- 多配置属性源 - @org.springframework.context.annotation.PropertySources

关联

- 储存对象 - org.springframework.core.env.MutablePropertySources （implements PropertySources）
- 关联方法 - org.springframework.core.env.ConfigurableEnvironment#getPropertySources()

### Spring 内建的配置属性源

内建 PropertySource

| PropertySource 类型                                          | 说明                      |
| ------------------------------------------------------------ | ------------------------- |
| org.springframework.core.env.CommandLinePropertySource       | 命令行配置属性源          |
| org.springframework.jndi.JndiPropertySource                  | JNDI 配置属性源           |
| org.springframework.core.env.PropertiesPropertySource        | Properties 属性配置源     |
| org.springframework.web.context.support.ServletConfigPropertySource | Servlet 配置属性源        |
| org.springframework.web.context.support.ServletContextPropertySource | ServletContext 配置属性源 |
| org.springframework.core.env.SystemEnvironmentPropertySource | 环境变量配置属性源        |

### 基于注解扩展 Spring 配置属性源

入口 - org.springframework.context.annotation.ConfigurationClassParser#doProcessConfigurationClass

- org.springframework.context.annotation.ConfigurationClassParser#processPropertySource

4.3 新增语义

- 配置属性字符编码 - encoding
- org.springframework.core.io.support.PropertySourceFactory

适配对象 - org.springframework.core.env.CompositePropertySource

### 基于 API 扩展 Spring 配置属性源

- 基于 Spring 应用上下文启动前装配 PropertySource
- 基于 Spring 应用上下文启动后装配 PropertySource

### 面试题

#### 简单介绍下 Spring Environment 接口

- 核心接口 - org.springframework.core.env.Environment
- 父接口 - org.springframework.core.env.PropertyResolver
- 可配置接口 - org.springframework.core.env.ConfigurableEnvironment
- 职责
  - 管理 Spring 配置属性源
  - 管理 Profiles

#### Environment 完整的生命周期是怎样的

## 第二十章 Spring 应用上下文声明周期

- Spring 应用上下文启动准备阶段
- BeanFactory 创建阶段
- BeanFactory 准备阶段
- BeanFactory 后置处理阶段
- BeanFactory 注册 BeanPostProcessor阶段
- 初始化内建 Bean: MessageSource
- 初始化内建 Bean: Spring 事件广播器
- Spring 应用上下文刷新阶段
- Spring 事件监听器注册阶段
- BeanFactory 初始化完成阶段
- Spring  应用上下文启动完成阶段
- Spring  应用上下文启动阶段
- Spring  应用上下文停止阶段
- Spring  应用上下文关闭阶段

### BeanFactory 创建阶段

AbstractApplicationContext#obtainFreshBeanFactory()方法

刷新 Spring 应用上下文底层 BeanFactory - refreshBeanFactiry()

- 销毁或者关闭 BeanFactory 如果存在的话
- 创建 BeanFactory - createBeanFactory()
- 设置 BeanFactory Id
- 设置 是否允许 BeanDefinition 重复定义 - customizeBeanFactory
- 设置 是否允许循环引用 - customizeBeanFactory
- 加载 BeanDefinition
- 关联新建 BeanFactory 到Spring 应用上下文

返回 Spring 应用上下文底层 BeanFactory - getBeanFactory()

### BeanFactory 准备阶段

AbstractApplicationContext#prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) 方法

- 关联 ClassLoader
- 设置 Bean 表达式处理器
- 添加 PropertyEditorRegistrar 实现 - ResourceEditorRegistrar
- 添加 Aware 回调接口 BeanPostProcessor 实现 - ApplicationContextAwareProcessor
- 忽略 Aware 回调接口作为依赖注入接口
- 注册 ResolvableDependency 对象 - BeanFactory;ResourceLoader;ApplicationEventPublisher; ApplicationContext
- 注册 ApplicationListenerDetector 对象
- 注册 LoadTimeWeaverAwareProcessor 对象(AOP)
- 注册单例对象 - Environemnt、Java System Properties 以及 OS 环境变量

### BeanFactory 后置处理阶段

AbstractApplicationContext#postProcessorBeanFactory(ConfigurableListableBeanFactory) 方法

- 由子类覆盖该方法

AbstractApplicationContext#invokeBeanFactoryPostProcessor(ConfigurableListableBeanFactory)方法

- 调用 BeanFactoryPostProcessor 或者 BeanDefinitionRegistry 后置处理方法
- 注册 LoadTimeWeaverAwareProcesor 对象

### BeanFactory 注册 BeanProcessor 阶段

AbstractApplicationContext#registerBeanPostProcessor(ConfigurableListableBeanFactory) 方法

- 注册 PriorityOrdered 类型的 BeanPostProcessor Beans
- 注册 Ordered 类型的 BeanPostProcessor Beans
- 注册普通 BeanPostProcessor Beans
- 注册 MergedBeanDefinitionPostProcessor Beans
- 注册 ApplicationListenerDetector 对象

### 初始化内建 Bean:MessageSource

AbstractApplicationContext#initMessageSource()方法

### 初始化内建 Bean:Spring 事件广播器

AbstractApplicationContext#initApplicationEventMulticaster()方法

见ApplicationEventPublisher 底层实现

### Spring 应用上下文刷新阶段

AbstractApplicationContext#onRefresh()方法

```java
protected void onRefresh() throws BeansException {
	// For subclasses: do nothing by default.
}
```

### Spring 事件监听器注册阶段

AbstractApplicationContext#registerListeners()方法

- 添加当前应用上下文所关联的 AplicationListener对象(集合)
- 添加 BeanFactory 所注册的 ApplicationListener Beans
- 广播早期 Spring 事件

### BeanFactory 初始化完成阶段

AbstractApplicationContext#finishBeanFactoryInitialization(ConfigurableListableBeanFactory)方法

- BeanFactory 关联 ConversionService 如果存在
- 添加 StringValueResolver 对象
- 依赖查找 LoadTimeWeaverAware Bean
- BeanFactory 临时 ClassLoader 置为 null
- BeanFactory  冻结配置
- BeanFactory  初始化非延迟单例 Beans

### Spring 应用上下文刷新完成阶段

AbstractApplicationContext#finishRefresh()方法

- 清除 ResourceLoader 缓存  - clearResourceCaches() @since 5.0
- 初始化 LifecycleProcessor对象 - initLifecycleProcessor()
- 调用 LifecycleProcessor#onRefresh()方法
- 发布 Spring 应用上下文已刷新事件 - ContextRefreshedEvent
- 向 MBeanServer 托管 Live Beans

### Spring 应用上下文启动阶段

AbstractApplicationContext#start()方法

- 启动 LifecycleProcessor: 依赖查找 Lifecycle Beans
- 启动 Lifecycle Beans

发布 Spring 应用上下文已启动事件 - ContextStartedEvent

### Spring 应用上下文停止阶段

AbstractApplicationContext#start()方法

- 停止 LifecycleProcessor: 依赖查找 Lifecycle Beans; 停止 Lifecycle Beans

发布 Spring 应用上下文已停止事件 - ContextStoppedEvent

### Spring 应用上下文关闭阶段

AbstractApplicationContext#close()方法

- 状态标识: active(false) close(true)
- Live Bean JMX 托管: LiveBeansView.unregisterApplicationContext(ConfigurableApplicationContext);
- 发布 Spring 应用上下文已关闭事件 - ContextClosedEvent
- 关闭 LifecycleProcessor:  依赖查找 Lifecycle Beans; 停止 Lifecycle Beans
- 销毁 Spring Beans
- 关闭 BeanFactory
- 回调 onClose()
- 注册 Shutdown Hook 线程(如果注册过)

### 依赖查找(注入)的 Bean 会被缓存吗

单例 Bean 会被缓存

缓存位置: org.springframework.beans.factory.support.DefaultSingletonBeanRegistry#singletonObjects

原型 Bean 不会

当前依赖查询或依赖注入时 根据 BeanDefinition每次创建

其他 Scope Bean

- request: 每个 ServletRequest 内部缓存, 生命周期维持在每次 HTTP 请求

- session: 每个 HttpSession 内存缓存, 生命周期维持在每个用户 HTTP 会话
- application: 当前 Servlet 应用内部缓存

### @Bean 的处理流程是怎样的

- 解析范围 - @Configuration Class 中的 @Bean方法
- 方法类型 - 静态 @Bean方法和实例 @Bean方法

@Bean >> BeanDefinition

- AnnotatedBeanDefinitionReader 
-  AnnotationConfigUtils.registerAnnotationConfigProcessors(BeanDefinitionRegistry)

- ConfigurationClassPostProcessor#postProcessBeanDefinitionRegistry(BeanDefinitionRegistry) 
- processConfigBeanDefinitions(BeanDefinitionRegistry)

- ConfigurationClassParser#retrieveBeanMethodMetadata(sourceClass)

- ConfigurationClass::addBeanMethod(BeanMethod)

- ConfigurationClassBeanDefinitionReader#loadBeanDefinitionsForBeanMethod(BeanMethod)

### BeanFactory 是如何处理循环依赖的

- 循环依赖开关 - AbstractAutowireCapableBeanFactory#setAllowCircularReferences
- 单例属性 - DefaultSingletonBeanRegistry#singletonFactories
- 获取早期未处理 Bean 方法 - AbstractAutowireCapableBeanFactory#getEarlyBeanReference
- 早期未处理 Bean 属性 -  DefaultSingletonBeanRegistry#earlySingletonObjects

### Bean 创建流程

![DefaultListableBeanFactory](https://cdn.qingweico.cn/DefaultListableBeanFactory.png)

![Bean 创建流程](https://cdn.qingweico.cn/Bean%20%E5%88%9B%E5%BB%BA%E6%B5%81%E7%A8%8B.png)

```properties
# xml properties yaml json -> BeanDefinition
# 读取配置信息核心抽象接口 
BeanDefinitionReader
```

BeanDefinition到实例化的过程还需要其它操作

在容器创建过程中需要动态的改变Bean的信息 比如替换掉配置文件的占位符

```properties
<property name="url" value="${jdbc.url}">
```

#### postProcessor

- BeanFactoryPostProcessor：用来增强 beanDefinition信息
  - 实现BeanFactoryPostProcessor接口 重写postProcessBeanFactory 修改beanDefinition信息

- BeanPostProcessor：用来增强 bean信息

```properties
# 处理配置文件占位符(BeanDefinition实例化之前) 实现了BeanFactoryPostProcessor
PlaceholderConfigurerSupport
# AbstractApplicationContext#refresh()#invokeBeanFactoryPostProcessors()进行处理
```

```properties
# 对各种注解的处理
ConfigurationClassPostProcessor
```

```properties
# @PostConstruct @PreDestroy
CommonAnnotationBeanPostProcessor
```

#### 实例化之前

- 准备 BeanPostProcesor
- 准备监听器 事件 广播器

```properties
# 实例化 到 初始化
填充属性
调用Aware方法接口
# postProcessBeforeInitialization
before 
调用init-method
# postProcessAfterInitialization
after
```

#### Aware接口的作用

Spring 容器分为 用户自定义容器 和 内建容器

若用户想获取容器对象, 即可调用xxxAware接口

```properties
# 比如 BeanFactoryAware 和 ApplicationContextAware
AbstractAutowireCapableBeanFactory#invokeAwareMethods
```

#### BeanFactory 和 FactoryBean 的区别

都是用来创建 Bean 对象的

BeanFactory 遵循完整的创建过程 这个过程是由 Spring 来管理的

FactoryBean 只需调用 getObjet 就可以返回具体的对象 整个对象的创建过程是由用户来控制的

#### BeanPostProcessor

动态代理 - > Bena 初始化后调用

```properties
# 实现了BeanPostProcessor
AbstractAutoProxyCreator#postProcessAfterInitialization(bean, beanName)
wrapIfNecessary(bean, beanName, cacheKey)
createProxy(beanClass, beanName, specificInterceptors, targetSource)
ProxyFactory#getProxy(classLoader)
createAopProxy().getProxy(classLoader)
AopProxy#getProxy(classLoader)
```

### 容器和对象的创建流程

- 先创建容器
- 加载配置文件 封装成 BeanDefinition
- 准备工作
  - 准备 BeanPostProcesor
  - 准备监听器 事件 广播器
- 实例化
- 初始化
- 获取到完整对象

```properties
# 准备工作 AbstractApplicationContext#refresh()
registerBeanPostProcessors(beanFactory)
initMessageSource()
initApplicationEventMulticaster()
onRefresh()
registerListeners()
```

JAVA 解析 XML 通常有两种方式:DOM 和SAX
