[TOC]

## Spring特性总览

### 核心特性

- IOC容器
- Spring事件
- 资源管理
- 国际化
- 校验
- 数据绑定
- 类型转换
- Spring表达式
- 面向切面编程

### 数据存储

- JDBC事务抽象
- DAO支持
- O/R映射
- XML编列(XML的序列化和反序列化)

### Web技术

Web Servler技术栈

- Spring MVC
- WebSocket
- SockJS

Web Reactive 技术栈

- Spring WebFlux
- WebClient
- WebSocket

### 技术整合

- 远程调用: RMI(Java标准的远程方法调用) Hessioan 开源协议 Dubbo基于该协议
- Java消息服务(JMS)
- Java连接架构(JCA)
- Java管理扩展(JMX)
- Java邮件客户端
- 本地任务
- 本地调度
- 缓存抽象
- Spring测试

Java1.3 引入了动态代理

Spring Boot1.x基于Spirng 4开发的 而SpringBoot 2.x是基于Spring 5开发的	

## Java版本依赖与支持

| Spring Framework版本 | Java标准版 | Java企业版           |
| -------------------- | ---------- | -------------------- |
| 1.x                  | 1.3+       | J2EE 1.3+            |
| 2.x                  | 1.4.2+     | J2EE 1.3+            |
| 3.x                  | 5+         | J2EE 1.4 和 Java EE5 |
| 4.x                  | 6+         | Java EE 6 和 7       |
| 5.x                  | 8+         | Java EE 7            |



## Spring 模块化设计(Modular)

- spring-aop
- spring-aspects
- spring-context-indexer
- spring-context-support
- spring-context
- spring-core
- spring-expression
- spring-instrument
- spring-jcl
- spring-jdbc
- spring-jms
- spring-messaging
- spring-orm
- spring-oxm
- spring-test
- spring-tx
- spring-web
- spring-webflux
- spring-webmvc
- spring-websocket

JAX-RS: Java标准的注解的方式驱动来开发REST服务

## Java语法变化

- 2004 JDK5 枚举、泛型、注解、装箱和拆箱

- 2006 JDK6 @Override接口

- 2011 JDK7 Diamond语法(<>)、多Catch、Try(ARM(Auto Resource Management)try-with-resources)

- 2014 JDK8 Lambad语法、可重复注解、类型注解

- 2017 JDK9 模块化、接口私有方法

- 2018 JDK10 局部变量类型推断(var)

  从Java9 和Java10开始 Spring Framework5里面并没有提供Java9和Java10之后语法或者API的支持

## Spring IOC 容器概述

- Spring IOC 依赖查找
- Spring IOC 依赖注入
- Spring IOC 依赖来源
- Spring IOC 配置元信息
- Spring IOC 容器
- Spring 应用上下文
- 使用Spring IOC容器
- Spring IOC 容器生命周期

### Spring IOC 依赖查找

- 根据Bean名称查找
  - 实时查找
  - 延迟查找
- 根据Bean类型查找
  - 单个Bean对象
  - 集合Bean对象
- 根据Bean名称 + 类型查找
- 根据Java注解查找
  - 单个Bean对象
  - 集合Bean对象

### Spring IOC 依赖注入

根据Bean 名称注入

根据 Bean 类型注入

- 单个Bean 对象
- 集合Bean 对象

注入容器内建Bean 对象

注入非Bean 对象

注入类型

- 实时注入
- 延迟注入

### Spring IOC 配置元信息

Bean定义配置

- 基于 XML文件
- 基于 Properties文件
- 基于 Java 注解
- 基于Java API

IOC 容器配置

- 基于 XML 文件
- 基于 Java注解
- 基于 Java API

外部化属性配置

- 基于Java配置

### Spring 应用上下文

ApplicationContext和BeanFactory的关系

ApplicationContext除了IOC的角色 还提供了

- 面向切面(AOP)
- 配置元信息
- 资源管理
- 事件
- 国际化
- 注解
- Environment抽象

BeanFactoyr是 Spring 底层 IOC容器

ApplicationContext 是具备应用特性的BeanFactory超集 

### Spring Bean 基础

#### 定义Spring Bean

什么是BeanDefinition

BeanDefinition是Spring Framework 中定义Bean的配置元信息接口

- Bean的类名
- Bean行为配置元素 如作用域、自动绑定的模式、生命周期回调等
- 其他Bean引用 又称为合作者或者依赖
- 配置设置 比如Bean属性

BeanDefinition元信息

| 属性                     | 说明                                            |
| ------------------------ | ----------------------------------------------- |
| Class                    | Bean 全类名 必须是具体类名 不能用抽象类或者接口 |
| Name                     | Bean 的名称或者ID                               |
| Scope                    | Bean 的作用域 比如 singleton、prototype         |
| Constructor arguments    | Bean 构造器参数(用于依赖注入)                   |
| Properties               | Bean 属性设置(用于依赖注入)                     |
| Autowiring mode          | Bean 自动绑定模式(如 通过名称 byName)           |
| Lazy initialization mode | Bean 延迟初始化模式(延迟和非延迟)               |
| Initialization method    | Bean 初始化回调方法名称                         |
| Destruction method       | Bean 销毁回调方法名称                           |

BeanDefinition 构建

- 通过 BeanDefinitionBuilder
- 通过 AbstractBeanDefinition 以及派生类

#### 命名 Spring Bean

Bean 的名称

- 每个Bean 拥有一个或者多个标识符 这些标识符在Bean 所在的容器必须是唯一的 通常 一个Bean仅有一个标识符 如果需要额外的 可考虑使用别名来扩充

- 在基于XML的配置元信息中(基于XML配置不一定是基于XML文件 可以是网络中XML或者本地文件) 可使用id或者name属性来规定Bean的标识符 通常Bean的标识符由字母组成 允许出现特殊字符 如果要想引入Bean的别名的话 可在name属性使用`,` 或者`;` 来间隔

- Bean的id或者name属性并非必须规定 如果留空的话 容器会为Bean自动生成一个唯一的名称

- Bean的命名尽管没有限制 不过建议采用驼峰的方式 更符合Java的命名约定

Bean名称生成器(BeanNameGenerator) 由Sping Framework 2.0.3引入 有两种实现

- DefaultBeanNameGenerator 默认通用的BeanNameGenerator实现
- AnnotationBeanNameGenerator 基于注解扫描的BeanNameGenerator实现

#### 注册 Spring Bean

BeanDefinition 注册

XML 配置元信息

- <bean name = "" />

Java 注解配置元信息

- @Bean
- @Component
- Import

Java API 配置元信息

- 命名方式: BeanDefinitionRegistry#registerBeanDefinition(String, BeanDefinition)
- 非命名方式 BeanDefinitionReaderUtils#(AbstractBeanDefinition definition, BeanDefinitionRegistry registry)
- 配置类方式: AnnotatedBeanDefinitionReader#register(Class<?>... componentClasses)

#### 实例化 Spring Bean

Bean 实例化

常规方式

- 通过构造器(配置元信息: XML Java注解和Java API)
- 通过静态工厂方法(配置元信息: XML Java注解和Java API)
- 通过Bean 工厂方法(配置元信息: XML Java注解和Java API)
- 通过 FactoryBean(配置元信息: XML Java注解和Java API)

特殊方式

- 通过 ServciceLoaderFactoryBean
- 通过AutowireCapableBeanFactory#createBean(Class, int, boolean)
- 通过 BeanDefintionRegistry#registerBeanDefinition(Stirng, BeanDefinition)

#### 初始化 Spring Bean

 Bean 初始化

- @PostConstruct 标注方法
- 实现 InitializingBean 接口的 afterPropertiesSet()方法
- 自定义初始化方法
  - XML 配置 : <bean init-method="init" />
  - Java 注解: @Bean(initMethod="")
  - Java API: AbstractBeanDefinition#setInitMethodName(String)

#### 延迟初始化 Spring Bean

Bean 延迟初始化

- XML 配置: <bean lazy-init="true"/>
- Java 注解 @Lazy 默认为true

#### 销毁 Spring Bean

Bean 销毁

- @PreDestroy
- 实现 DisposableBean 接口的destroy() 方法
- 自定义销毁方法
  - XML 配置: <bean destroy=""/>
  - Java 注解: @Bean(destroy="")
  - Java API AbstractBeanDefinition#setDestroyMethodName(String）

#### Bean 垃圾回收

- 关闭 Spring 容器
- 执行 GC
- Spring Bean 覆盖的finalize() 方法被调用

Spring IOC 依赖查找

单一类型依赖查找

- 根据 Bean 名称查找
  - getBean(String)
  - Spering 2.5 覆盖默认参数: getBean(String , Object...)
- 根据 Bean 类型查找
  - 实时查找
    - Spring 3.0 getBean(Class)
    - Spring 4.1 覆盖默认参数: getBean(Class, Object...)
  - Spring 5.1 延迟查找
    - getBeanProvider(Class)
    - getBeanProvider(Resolvable Type)
- 根据 Bean 名称 + 类型查找: getBean(String,  Class)

