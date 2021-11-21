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

```xml
<bean name = "" />
```

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
  - XML 配置:
  
    ```xml
     <bean init-method="init" />
    ```
  
  - Java 注解: @Bean(initMethod="")
  
  - Java API: AbstractBeanDefinition#setInitMethodName(String)

#### 延迟初始化 Spring Bean

Bean 延迟初始化

- XML 配置:

  ```xml
   <bean lazy-init="true"/>
  ```

- Java 注解 @Lazy 默认为true

#### 销毁 Spring Bean

Bean 销毁

- @PreDestroy

- 实现 DisposableBean 接口的destroy() 方法

- 自定义销毁方法
  - XML 配置: 
  
    ```xml
    <bean destroy=""/>
    ```
  
  - Java 注解: @Bean(destroy="")
  
  - Java API: AbstractBeanDefinition#setDestroyMethodName(String）

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

## Spring IOC 依赖查找

### 集合类型依赖查找

集合类型依赖查找接口: ListableBeanFactory

根据 Bean 类型查找

- 获取同类型 Bean 名称列表
  - getBeanNamesForType(Class)
  - Spring 4.2 getBeanNamesForType(ResolvableType)
- 获取同类型Bean 实例列表
  - getBeansOfType(Class) 以及重载方法

通过注解类型查找

- Spring 3.0 获取标注类型 Bean 名称列表
  - getBeanNamesForAnnotation(Class<? extends Annotation>)
- Spring 3.0 获取标注类型 Bean 实例列表
  - getBeansWithAnnotation(Class<? extends Annotation>)
- Spring 3.0 获取指定名称 + 标注类型 Bena 实例
  - findAnnotationOnBean(String, Class<? extends Annotation>)

### 层次性依赖查找

层次性依赖查找接口: HierarchicalBeanFactory

双亲 BeanFactory: getParentBeanFactory()

层次性查找

- 根据 Bean 名称查找  基于 containsLocalBean 方法实现
- 根据 Bean 类型查找实例列表
  - 单一实例: BeanFactoryUtils#beanOfType
  - 集合类型: BeanFactoryUtils#beansOfTypeIncludingAncestors
- 根据 Java 注解查找名称列表
  - BeanFactoryUtils#beanNamesForTypeIncludingAncestors

### 安全依赖查找

| 依赖查找类型 | 代表实现                           | 是否安全 |
| ------------ | ---------------------------------- | -------- |
| 单一类型查找 | BeanFactory#getBean                | 否       |
|              | ObjectFactory#getObject            | 否       |
|              | ObjectProvider#getIfAvailable      | 是       |
| 集合类型查找 | ListableBeanFactory#getBeansOfType | 是       |
|              | ObjectProvider#stream              | 是       |

### 内建可查找的依赖

AbstractApplicationContext内建可查找的依赖

Spring 内建单例对象

| Bean 名称                   | Bean 实例                        | 使用场景                |
| --------------------------- | -------------------------------- | ----------------------- |
| environment                 | Environment 对象                 | 外部化配置以及 Profiles |
| systemProperties            | java.utilProperties 对象         | Java 系统属性           |
| systemEnvironment           | javautil.Map 对象                | 操作系统环境变量        |
| messageSource               | MessageSource 对象               | 国际化文案              |
| lifecycleProcessor          | LifecycleProcessor 对象          | Lifecycle Bean 处理器   |
| applicationEventMulticaster | ApplicationEventMulticaster 对象 | Spring 事件广播器       |

注解驱动 Spring 应用上下文内建可查找的依赖

org.springframework.context.annotation.AnnotationConfigUtils类中

| Bean名称                                 | Bean 实例                                  | 使用场景                                               |
| :--------------------------------------- | ------------------------------------------ | ------------------------------------------------------ |
| internalConfigurationAnnotationProcessor | ConfigurationClassPostProcessor 对象       | 处理 Spring 配置                                       |
| internalAutowiredAnnotationProcessor     | AutowiredAnnotationBeanPostProcessor对象   | 处理 @Autowired 以及 @Value注解                        |
| internalCommonAnnotationProcessor        | CommonAnnotationBeanPostProcessor对象      | (条件激活) 处理 JSR-250 注解 如 @PostConstruct等       |
| internalEventListenerProcessor           | EventListenerMethodProcessor对象           | 处理标注 @EventListener 的Spring 事件监听方法          |
| event.internalEventListenerFactory       | DefaultEventListenerFactory对象            | @ EventListener 事件监听方法适配为 ApplicationListener |
| internalPersistenceAnnotationProcessor   | PersistenceAnnotationBeanPostProcessor对象 | (条件激活) 处理 JPA 注解场景                           |

### 依赖查找中的经典异常

| 异常类型                        | 触发条件                              | 场景举例                                     |
| ------------------------------- | ------------------------------------- | -------------------------------------------- |
| NoSuchBeanDefinitionException   | 当查找Bean 不存在于 IOC 容器时        | BeanFactory#getBean; ObjectFactory#getObject |
| NoUniqueBeanDefinitionException | 类型查找时 IOC 容器存在多个 Bean 实例 | BeanFactory#getBean(Class)                   |
| BeanInstantiationException      | 当 Bean 所对应的类型为非具体类时      | BeanFactory#getBean                          |
| BeanCreationException           | 当 Bean 初始化过程中                  | Bean 初始化方法执行异常时                    |
| BeanDefinitionStoreException    | 当 BeanDefinition 配置元信息非法时    | XML 配置资源无法打开时                       |

ObjetcFactory 和 BeanFactory 的区别

两者都提供了 依赖查找 的能力

ObjectFactory仅关注一个或者一种类型的 Bean 依赖查找 且自身并不具备依赖查找的能力 能力则由 BeanFactory 输出

BeanFactory 则提供了单一类型、集合类型以及层次性等多种依赖查找方式

BeanFactory.getBean 操纵是否线程安全

BeanFactory.getBean 方法的执行是线程安全的 操作过程中会增加互斥锁(syn)

## Spring 依赖注入

依赖注入的模式和类型

手动类型

- XML 资源配置元信息
- Java 注解配置元信息
- API 配置元信息

自动模式 实现方提供自动关联的方式 按照内建的注入规则

- Autowiring(自动绑定)

###  自动绑定模式

| 模式        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| no          | 默认值 未激活Autowiring 需要手动指定依赖注入对象             |
| byName      | 根据被注入属性的名称作为 Bean 名称进行依赖查找 并将对象设置到该属性 |
| byType      | 根据被注入属性的类型作为依赖类型进行查找 并将对象设置到该属性 |
| constructor | 特殊 byType 类型 用于构造器参数                              |

### 接口回调注入

Aware 系列接口回调

| 内建接口                     | 说明                                                   |
| ---------------------------- | ------------------------------------------------------ |
| BeanFactoryAware             | 获取 IOC 容器  BeanFactory                             |
| ApplicationContextAware      | 获取 Spring 应用上下文 ApplicationContext对象          |
| EnvironmentLoaderAware       | 获取 Environment 对象                                  |
| ResourceLoaderAware          | 获取资源加载对象  ResourceLoader                       |
| BeanClassLoaderAware         | 获取加载当前 Bean Class 的 ClasssLoader                |
| BeanNameAware                | 获取当前 Bean 的名称                                   |
| MessageSourceAware           | 获取 MessageSource 对象 用于 Spring 国际化             |
| ApplicationEventPublishAware | 获取 ApplicationEventPublishAware 对象 用于Spring 事件 |
| EmbeddedValueResolverAware   | 获取 StringValueResolver 对象用于占位符处理            |

### 依赖注入选择

- 低依赖: 构造器注入(参数较少时)
- 多依赖: Setter 方法注入(不能保证顺序)
- 便利性: 字段注入
- 声明类: 方法注入

### 基础类型注入

- 原生类型
- 标量类型: Number、 Character、 Boolean、 Enum、 Locale、Charset   、Currency、Properties 、UUID
- 常规类型: Object 、String 、TimeZone、Calendar、Optional
- Spring 类型: Resource、InputSource、Formatter

### 限定注入

使用 @Qualifier注解

- 通过 Bean 名称限定
- 通过分组限定

基于注解 @Qualifier 扩展限定

- 自定义注解 如 Spring Cloud @LoadBalanced

### 延迟注入

使用 API ObjectFactory 延迟注入

- 单一类型
- 集合类型

使用 API ObjectProvider 延迟注入(推荐)

- 单一类型
- 集合类型

### 依赖处理过程

- 入口 DefaultListableBeanFactory#resolveDependency
- 依赖描述符  DependencyDescriptor
- 自定绑定候选对象处理器 AutowireCandidateResolver

### @Autowired 注入

- 元信息解析
- 依赖查找
- 依赖注入(字段、方法)

AutowiredAnnotationBeanPostProcessor

用来处理@Autowired和JSR-330@Inject注解

CommonAnnotationBeanPostProcessor

用来处理更为通用的注解

### Java通用注解注入原理

CommonAnnotationBeanPostProcessor

注入注解

- java.xml.ws.WebServiceRef
- javax.ejb.EJB
- javax.annotation.Resource

生命周期注解

- javax.annotation.PostConstruct
- javax.annotation.PreDestroy

### 自定义依赖注解

基于 AutowiredAnnotationBeanPostProcessor 实现

自定义实现

1 生命周期处理

- InstantiationAwareBeanPostProcessor
- MergedBeanDefinitionPostProcessor

2 元数据

- InjectedElement
- InjectionMetadata

### 面试题

有多少种依赖注入的方式

- 构造器注入
- Setter注入
- 字段注入
- 方法注入
- 接口回调注入

构造器注入好还是Setter注入好

没有最好的方案 只有相对的合理

两种依赖注入的方式均可使用 如果是必须依赖的话 推荐使用构造器注入 Setter用于可选依赖

如果依赖的参数比较少的话 推荐使用构造器注入 构造器注入可以保证线程安全 确保数据的一致性

## 依赖来源

### 依赖查找来源

Spring BeanDefinition

- ```xml
  <bean id="user" class=""/>
  ```

- @Bean public User user(){}

- BeanDefinitionBuilder

单例对象 

- API实现

### 依赖注入的来源

Spring BeanDefinition

- ```xml
  <bean id="user" class=""/>
  ```

- @Bean public User user(){}

- BeanDefinitionBuilder

单例对象 

- API实现

非 Spring 容器管理对象(ResolvableDependency)

Spring 容器管理和游离对象

| 来源                  | Spring Bean 对象 | 生命周期管理 | 配置元信息 | 使用场景           |
| --------------------- | ---------------- | ------------ | ---------- | ------------------ |
| Spring BeanDefinition | 是               | 是           | 有         | 依赖查找、依赖注入 |
| 单体对象              | 是               | 否           | 无         | 依赖查找、依赖注入 |
| ResolvableDependency  | 否               | 否           | 无         | 依赖注入           |

### Spring BeanDefinition作为依赖注入来源

要素

- 元数据: BeanDefinition
- 注册: BeanDefinitionRegistry#registerBeanDefinition
- 类型: 延迟和非延迟
- 顺序: Bean 生命周期顺序按照注册顺序

### 单例对象作为依赖来源

要素:

- 来源: 外部普通Java对象(不一定是 POJO)
- 注册: SingletonBeanRegistry@registerSingleton

限制

- 无生命周期管理
- 无法实现延迟初始化 Bean

### 非 Spring 容器管理对象作为依赖来源

要素

- 注册: ConfigurableListableBeanFactory#registerResolvableDependency

限制

- 无生命周期管理
- 无法实现延迟加载
- 无法通过依赖查找
- 只能用于类型方法方面的注入 不能用于名称方面的注入

### 面试题

注入和查找的依赖来源是否相同

否 依赖查找的来源仅限于 Spring BeanDefinition 以及单例对象 而依赖注入的来源还包括 Resolvable Dependency 以及 @Value 所标注的外部化配置

单例对象能在 IOC 容器启动后注册吗

可以的 单例对象 与 BeanDefinition 不同 BeabDefinition 会被ConfigurableListableBeanFactory#freezeConfiguration()方法影响，从而冻结注册 单例对象则没有这个限制

Spring 依赖注入的来源有哪些

- Spring BeanDefinition
- 单例对象
- Resolvable Dependency: ConfigurableListableBeanFactory#registerResolvableDependency
- @Value 外部化配置: @Autowired 和 @Value 在AutowiredAnnotationBeanPostProcessor处理

## Spring Bean 作用域

| 来源        | 说明                                                      |
| ----------- | --------------------------------------------------------- |
| singleton   | 默认 Spring Bean 作用域 一个 BeanFactory 有且仅有一个实例 |
| prototype   | 原型作用域 每次依赖查找和依赖注入生成新的Bean对象         |
| request     | 将 Spring Bean 储存在 ServletRequest 上下文中             |
| session     | 将 Spring Bean 储存在 HttpSession上下文中                 |
| application | 将 Spring Bean 储存在 ServletContext 上下文中             |

### prototype Bean作用域

Spring 容器 没有办法管理 prototype Bean 的完整的生命周期 也没有办法记录实例的存在 销毁回调方法将不会执行 可以利用 BeanPostProcessor 进行清扫工作

### requets Bean作用域

配置

- XML

  ```xml
   <bean class="" scope="request"/>
  ```

- Java 注解 @RequestScope 或者 @Scope(WebApplicationContext.SCOPE_REQUEST)

实现

- API RequestScope

### session Bean作用域

配置

- XML

  ```xml
   <bean class="" scope="session"/>
  ```

- Java 注解 @SessionoScope 或者 @Scope(WebApplicationContext.SCOPE_SESSION)

实现

- API SessionScope 

### application Bean作用域

配置

- XML 

  ```xml
  <bean class="" scope="application "/>
  ```

- Java 注解 @ApplicationScope 或者 @Scope(WebApplicationContext.SCOPE_APPLICATION)

实现

- API ServletContextScope

### 自定义 Bean 作用域

实现 Scope

- org.springframework.beans.factory.config.Scope

注册 Scope(两种方式都行)

- API org.springframework.beans.factory.config.ConfigurableBeanFactory#registerScope

- 配置

  ```xml
  <bean class="org.springframework.beans.factory.config.CustomScopeConfigurer">
      <property name="scopes">
          <map>
              <entry key="..." value-ref="...">
              </entry>
          </map>
      </property>
  </bean>
  ```

  

### 面试题

Spring 内建的 Bean 作用域有几种

- singleton
- prototype
- request
- session
- application
- websocket

singleton Bean 在一个应用中是否是唯一的

否 singleton bean 仅在当前 Spring IOC 容器中(Bean Factory)是单例对象

因为整个应用可能包含多个应用上下文, 比如 一个静态字段在JVM中是否唯一呢 答案也是否 因为一个静态字段对于加载它的ClassLoader是唯一的, 但是一个应用可以有多个ClassLoader

application Bean 是否被其他方案替代?

可以的 实际上 application Bean 与 singleton Bean 没有本质区别
