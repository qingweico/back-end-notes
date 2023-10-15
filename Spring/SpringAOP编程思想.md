[TOC]

## 第一章 Spring AOP 总览

### AOP 引入: OOP 存在哪些局限性

静态化语言: 类的结构一旦定义 不容易(并不是无法修改)被修改

侵入性扩展: 通过继承和组合组织新的类结构

### AOP 常见使用场景

日志场景

- 诊断上文 - 如 log4j  或者 logback 中的 MDC(映射诊断上下文)
- 辅助信息 - 如方法的执行时间

统计场景

- 方法调用次数
- 执行异常次数
- 数据抽样
- 数值累加

安防场景

- 熔断 Netflix Hystrix
- 限流和降级 Alibaba Sentinel
- 认证和授权 Spring Security
- 监控 JMX(Java Management Extension)

性能场景

- 缓存 Spring Cache
- 超时控制

### Java AOP 设计模式

- 代理模式: 静态代理和动态代理
- 判断模式: 类 方法 注解 参数 异常...
- 拦截模式: 前置 后置 返回 异常

### Java AOP 代理模式

Java 静态代理

- 常用 OOP 继承和组合相结合

Java 动态代理

- JDK 动态代理
- 字节码提升 如 CGLIB

### Java AOP 判断模式

判断来源

- 类型
- 方法
- 注解
- 参数
- 异常

### Java AOP 拦截器模式(Interceptor)

拦截类型

- 前置拦截
- 后置拦截
- 异常拦截

### Spring AOP 功能概述

核心特性

- 纯 Java 实现 无编译时特殊处理 不修改和控制 ClassLoader
- 仅支持方法级别的 Join Points
- 非完整 AOP 实现框架
- Spring IOC 容器整合
- AspectJ 注解驱动整合(非竞争关系)

### Spring AOP 编程模型

注解驱动

- 实现 Enable 模块驱动 @EnableAspectJAutoProxy
- 注解
  - 激活 AspectJ 自动代理 - @EnableAspectJAutoProxy
  - Aspect - @Aspect
  - Pointcut - @Pointcut 
  - Advice - @Before @AfterReturing @AfterThrowing @After @Aroud
  - Introduction - @DeclareParents

XML  配置驱动

- 实现 Spring Extensible XML Authoring
- XML 元素
  - 激活 AspectJ 自动代理 `<aop:aspectj-autoproxy>`
  - 配置 `<aop:config>`
  - Aspect `<aop:aspect>`
  - Pointcut `<aop:pointcut>`
  - Advice `<aop:aroud/>` `<aop:before/>` `<aop:after-returing/>` `<aop:after-throwing/>` `<aop:after/>`
  - Introduction `<aop:declare-parents/>`
  - 代理 Scope `<aop:scoped-proxy>/`

### Spring AOP 设计目标

Sprng AOP 由三部分组成

- Java 动态代理
- 字节码提升
- AspectJ的整合

Spring 和 AspectJ 框架相辅相成 来提供一种完整的AOP解决方案

因为 AspectJ 没有 IOC 这样的企业级特性 只是在语言层面上帮助 Java OOP 实现了 AOP的特性

Spring 通过 AspectJ 去无缝地整合 Spring IOC 和 Spring IOC

Spring中的AspectJ 和 AspectJ框架不是一回事 后者的有些特性在Spring中并没有完完整整地实现

### JDK 动态代理

为什么 Proxy.newProxyInstance 会生成新的字节码

### CGLIB 动态代理

为什么 Java 动态代理无法满足 AOP 的需要

### 面试题

#### Spring AOP 和 AspectJ AOP存在哪些区别

- AspectJ  是 AOP 完整实现 Spring AOP则是部分实现

- Spring AOP 比 AspectJ 使用简单
- Spring AOP 整合 AspectJ 注解 与 IOC 容器
- Spring AOP 仅支持基于代理模式的 AOP
- Spring AOP 仅支持方法级别的 Pointcut

## 第二章 Spring AOP 基础

### 编程方式创建@AspectJ 代理

实现类 - org.springframework.aop.aspectj.annotation.AspectJProxyFactory 

### XML 配置驱动 - 创建 AOP 代理

实现方法

- 配置 org.springframework.aop.framework.ProxyFactoryBean
- Spring Scheam-Based 配置
  - `<aop:config>`
  - `<aop:aspectj-autoproxy>`

### 标准代理工厂 API

实现类 - org.springframework.aop.framework.ProxyFactory

### @AspectJ Pointcut 指令与表达式

Pointcut 是一个筛选工作 而 Advice 是个具体执行动作

一个 Pointcut  对应多个 Advice  实现

### XML 配置 Pointcut

XML 配置

- `<aop:pointcut>`

### API 实现 Pointcut

核心API - org.springframework.aop.Pointcut

- org.springframework.aop.ClassFilter
- org.springframework.aop.MethodMatcher

适配实现 - org.springframework.aop.support.DefaultPointcutAdvisor

### @AspectJ 拦截动作

注解 @Around 与 @Pointcut 有什么区别

### API 实现 Around Advice

思考: 为什么 Spring AOP 不需要设计 Around Advice

线索: 

-  AspectJ 中 @Around 与 org.aspectj.lang.ProceedingJoinPoint 配合执行被代理方法
- ProceedingJoinPoint#proceed() 方法类似于 Java Method#invoke(Object, Object...)
- Spring AOP 底层 API ProxyFactory 可通过 addAvcide 方法于 Advice 实现关联
- 接口 Advice 是 Interceptor 的父接口 而接口 MethodInterceptor 扩展了 Interceptor
- MethodInterceptor 的 invoke 方法参数 MethodInvocation 与 ProceedingJoinPoint 类似

![aop-api](https://cdn.qingweico.cn/aop-api.jpg)

### @AspectJ 前置动作

- 在同一个类中 @Around 优先级高于 @Before

- 可以通过 Ordered 接口改变多个@Before的执行顺序

### XML 配置Before Advice

XML 元素 `<aop:config>`

声明规则

- `<aop:config>`
- `<aop:aspect>`
- `<aop:before>`

属性设置(来源于 Spring AOP Schema 类型 basicAdviceType)

- pointcut: Pointcut表达式内容
- pointcut-ref: Pointcut表达式id(名称)

### API 实现 Before Advice

核心接口 - org.springframework.aop.BeforeAdvice

类型: 标记接口 与 org.aopalliance.aop.Advice 类似

方法 JoinPoint 扩展 - org.springframework.aop.MethodBeforeAdvice

接受对象 - org.springframework.aop.framework.AdvisedSupport

- 基础实现 - org.springframework.aop.framework.ProxyCreatorSupport

ProxyCreatorSupport常见实现类

- org.springframework.aop.aspectj.annotation.AspectJProxyFactory - 基于AspectJ
-  org.springframework.aop.framework.ProxyFactory - 基于Java API
- org.springframework.aop.framework.ProxyFactoryBean - 基于 注解或者XML配置

### @AspectJ 后置动作

After Advice 注解

- 方法返回后 - org.aspectj.lang.annotation.AfterReturning - 最后执行
- 异常发生后 - org.aspectj.lang.annotation.AfterThrowing - 方法执行异常时执行, 其他注解中的异常也会触发(比如@Before)
- finally执行 - org.aspectj.lang.annotation.After - 相当于finally

### XML 配置 After Advice

和XML 配置Before Advice一样

### API 实现 三种After Advice

核心接口 - org.springframework.aop.AfterAdvice

扩展

- org.springframework.aop.AfterReturningAdvice
- org.springframework.aop.ThrowsAdvice

其他与Before Advice一致

### 自动动态代理

代表实现

- org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator
- org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator
- org.springframework.aop.aspectj.annotation.AnnotationAwareAspectJAutoProxyCreator

Advisor = Advice 动作 + Pointcut

### 替换 TagetSource

### 面试题

#### Spring AOP 支持那些类型的 Advice

- Around Advice
- Before Advice
- After Advice
  - After
  - AfterReturning
  - AfterThrowing

#### Spring AOP 编程模型有哪些 代表组件有哪些

- 注解驱动: 解释和整合 AspectJ 注解 如@EnableAspectJAutoProxy
- XML: 配置 AOP 与 XML(Schema-Based) 相结合
- API 编程: 如 Joinpoint Pointcut Advice 和 ProxyFactory等

## 第三章 Spring AOP API 设计与实现

### Spring AOP API 整体设计

- Join point - Joinpoint
- Pointcut - Pointcut
- Advice 动作执行 - Advice
- Advice 容器 - Advisor
- Introduction - IntroductionInfo
- 代理对象创建基础类 - ProxyCreatorSupport
- 代理工厂 - ProxyFactory 、ProxyFactoryBean
- AopProxyFactory 配置管理器 - AdvisedSupport
- IOC 容器自动代理抽象 - AbstractAutoProxyCreator

### 接入点接口 - joinpoint

Interceptor 执行上下文 - Invocation

- 方法拦截器执行上下文 - MethodInvocation
- 构造器拦截器执行上下文 - ConstructorInvocation(Spring中仅支持方法级别的拦截 所以Spring中未实现 但AspectJ中已实现)

MethodInvocation

- ReflectiveMethodInvocation - 基于反射

- CglibMethodInvocation - 基于CGLIB

### Joinpoint 条件接口 - Pointcut

核心组件

- 类过滤器 - ClassFilter
- 方法匹配器 - MethodMatcher

### Pointcut 操作

组合实现 - org.springframework.aop.support.ComposablePointcut

工具类

- ClassFilter 工具类 - ClassFilters
- MethodMatcher 工具类 - MethodMatchers
- Pointcut 工具类 - Pointcuts

### Pointcout 便利实现

- 静态Pointcut - org.springframework.aop.support.StaticMethodMatcherPointcut
- 正则表达式 - org.springframework.aop.support.JdkRegexpMethodPointcut
- 控制流 - org.springframework.aop.support.ControlFlowPointcut

### Pointcut AspectJ 实现

- 实现类 - org.springframework.aop.aspectj.AspectJExpressionPointcut
- 指令支持 - SUPPORTED_PRIMITIVES字段
- 表达式 - org.aspectj.weaver.tools.PointcutExpression

### Joinpoint Before Advice 标准实现

标准实现: 在 Spring AOP 未引入 AspectJ 之前的内部实现

接口

- 标准接口 - org.springframework.aop.BeforeAdvice
- 方法级别 - org.springframework.aop.MethodBeforeAdvice

实现

- org.springframework.aop.framework.adapter.MethodBeforeAdviceInterceptor
- 所有的 BeforeAdvide(MethodBeforeAdvide) 都来自于 MethodBeforeAdviceInterceptor

### Joinpoint Before Advice AspectJ 实现

- 实现类 - org.springframework.aop.aspectj.AspectJMethodBeforeAdvice、

### Joinpoint AfterAdvice AspectJ 实现

接口

- org.springframework.aop.AfterAdvice
- org.springframework.aop.AfterReturningAdvice
- org.springframework.aop.ThrowsAdvice

实现

- org.springframework.aop.aspectj.AspectJAfterAdvice
- org.springframework.aop.aspectj.AspectJAfterReturningAdvice
- org.springframework.aop.aspectj.AspectJAfterReturningAdvice

### Advice 容器接口 - Advisor

接口 - AfterReturningAdvice

通用实现 - org.springframework.aop.support.DefaultPointcutAdvisor

Advisor 是 Advice 的一个容器

### Pointcut 与 Advice 连接器 - PointcutAdvisor

接口 - org.springframework.aop.PointcutAdvisor

通用实现

- org.springframework.aop.support.DefaultPointcutAdvisor

AspectJ实现

- org.springframework.aop.aspectj.AspectJExpressionPointcutAdvisor

- org.springframework.aop.aspectj.AspectJPointcutAdvisor

静态方法实现

- org.springframework.aop.support.StaticMethodMatcherPointcutAdvisor

IOC 容器实现

- org.springframework.aop.support.AbstractBeanFactoryPointcutAdvisor

### Introduction 与 Advice 连接器

接口

- org.springframework.aop.IntroductionAdvisor

元信息

- org.springframework.aop.IntroductionInfo

通用实现

- org.springframework.aop.support.DefaultIntroductionAdvisor

AspectJ实现

- org.springframework.aop.aspectj.DeclareParentsAdvisor

IntroductionAdvisor 与 PointcutAdvisor 的区别是 前者只过滤类型 不过滤方法

### AOP 代理接口 - AopProxy

接口

- org.springframework.aop.framework.AopProxy

实现

JDK 动态代理

- org.springframework.aop.framework.JdkDynamicAopProxy

CGLIB字节码提升

- org.springframework.aop.framework.CglibAopProxy
  - org.springframework.aop.framework.ObjenesisCglibAopProxy

### AopProxy 工厂接口与实现

接口

- org.springframework.aop.framework.AopProxyFactory
- 方法AopProxy createAopProxy(AdvisedSupport config) 中参数AdvisedSupport 译为被通知的支持对象

默认实现

- org.springframework.aop.framework.DefaultAopProxyFactory

  返回类型

  - org.springframework.aop.framework.JdkDynamicAopProxy

  - org.springframework.aop.framework.CglibAopProxy
    - org.springframework.aop.framework.ObjenesisCglibAopProxy

Spring AOP 提供了一种扩展能力 - 在ProxyCreatorSupport类中创建

### JDK AopProxy 实现 - JdkDynamicAopProxy

实现

- org.springframework.aop.framework.JdkDynamicAopProxy

配置

- org.springframework.aop.framework.AdvisedSupport

来源

- org.springframework.aop.framework.DefaultAopProxyFactory

### CGLIB AopProxy 实现 - CglibAopProxy

实现

- org.springframework.aop.framework.CglibAopProxy

配置

- org.springframework.aop.framework.AdvisedSupport

来源

- org.springframework.aop.framework.DefaultAopProxyFactory

## 第四章 Spring AOP 设计模式

GOF 23

 ============ 创建型  ============

### 抽象工厂模式

基本概念

- 抽象工厂模式提供了一种方式 可以将一组具有同一主题的单独的工厂封装起来

Spirng AOP 实现

- 接口 - org.springframework.aop.framework.AopProxyFactory
- 实现 - org.springframework.aop.framework.DefaultAopProxyFactory

### 构建器模式

基本概念

- 是一种对象构建模式 它可以将复杂对象的建造过程抽象出来 使这个抽象过程的不同实现方法可以构造不同表现(属性)的对象  比如StringBuilder

Spring AOP 实现

- 实现 -  org.springframework.aop.aspectj.annotation.BeanFactoryAspectJAdvisorsBuilder

### 工厂方法

基本概念

- 处理在不指定对象具体类型的情况下创建对象的问题
- 工厂方法模式的实质是定义一个创建对象的接口 但让实现这个接口的类来决定实列化哪个类
- 工厂方法让类的实列化推迟到子类中进行
- 工厂方法包括静态的(类)和动态的(对象)

Spring AOP 实现

- 实现 - org.springframework.aop.framework.ProxyFactory

与抽象工厂不同的是 抽象工厂必须抽象的 即是一个抽象类或者是接口

### 原型模式

基本概念

- 通过复制一个已经存在的实例来返回新的实例 而不是新建实例 被复制的实例就是原型 且这个原型是可以定制的
- 原型模式多用于创建复杂的或者耗时的实例 因为一个已经存在的实例使程序运行的更高效

Spring AOP 实现

- 实现 - org.springframework.aop.target.PrototypeTargetSource

### 单例模式

Spring AOP 实现

- 实现 - org.springframework.aop.target.SingletonTargetSource

 ============ 结构型  ============

### 适配器模式

基本概念

- 将一个类的接口转接成用户所期待的
- 一个适配使得因接口不兼容而不能在一起工作的类能在一起工作
- 将类自己的接口包裹在一个已存在的类中

Spring AOP 举例实现

- 实现 - org.springframework.aop.framework.adapter.AdvisorAdapter
- 适配对象 - org.aopalliance.aop.Advice
- 目标对象 -org.aopalliance.intercept.MethodInterceptor

### 组合模式

概念

- 把一组对象当作一个对象来处理

Spring AOP 实现

- 实现 - org.springframework.aop.support.ComposablePointcut
- 接口 - org.springframework.aop.Pointcut
- 成员 - org.springframework.aop.Pointcut

### 装饰器模式

概念

- 一种动态地往一个类中添加新的行为的设计模式
- 修饰模式相比生成子类更为灵活 这样可以给某个对象而不是整个类添加一些功能
- 通常装饰者和被装饰者之间是一个兄弟关系 或者是同一个父类(父接口)下面的

Spring AOP 实现

- 实现 - org.springframework.aop.aspectj.annotation.LazySingletonAspectInstanceFactoryDecorator

JDK 实现

- InputStream

Servlet 实现

- javax.servlet.ServletRequestWrapper

### 享元模式(Flyweight)

概念

- 通过使用对象减少内存的使用量
- 共享数据 缓存

Spring AOP 实现

- 实现 - org.springframework.aop.framework.adapter.AdvisorAdapterRegistry

### 代理模式

Spring AOP 实现

- 实现 - org.springframework.aop.framework.AopProxy
- org.springframework.aop.framework.CglibAopProxy
- org.springframework.aop.framework.JdkDynamicAopProxy

 ============ 行为型  ============

### 模板方法模式

基本概念

- 所谓的模板方法是一个定义在父类中的方法
- 父类中的方法可能是抽象方法 没有具体的动作 专门由子类去实现
- 父类中已经安排好了方法的执行顺序 
- 使用模板方法最好有两种及两种以上的实现方法 才能体现模板方法的价值

Spring AOP 中的实现

- 模板类 - org.springframework.aop.framework.autoproxy.AbstractAutoProxyCreator
- 模板方法 - getAdvicesAndAdvisorsForBean
- 子类实现
  - org.springframework.aop.framework.autoproxy.InfrastructureAdvisorAutoProxyCreator(XML)
  - org.springframework.aop.aspectj.annotation.AnnotationAwareAspectJAutoProxyCreator(注解)

JDK 中实现

- AQS

### 责任链模式

基本概念

- 包含了一些命令对象和一系列的处理对象
- 描述了往该处理链的末尾添加新的处理对象的方法

Spring AOP 实现

- 接口 - org.springframework.aop.framework.AdvisorChainFactory
- 实现 - org.springframework.aop.framework.DefaultAdvisorChainFactory

### 观察者模式

基本概念

Spring AOP 实现

- 观察者 -  org.springframework.aop.framework.ProxyCreatorSupport
- 被观察者 - org.springframework.aop.framework.AdvisedSupportListener
- 通知对象 - org.springframework.aop.framework.AdvisedSupport

### 策略模式(Strategy)

基本概念

- 指对象有某个行为 在不同的场景中有不同的实现

Spring AOP 实现举例

- org.springframework.aop.framework.DefaultAopProxyFactory#createAopProxy
- org.springframework.aop.config.ConfigBeanDefinitionParser#getAdviceClass

静态 

- if else

动态

- JDK SPI

### 命令模式

基本概念

- 把行动和参数封装起来
- 这些行动可以重复执行 取消以及取消后重做

Spring AOP 实现

- org.aopalliance.intercept.MethodInvocation
- org.aspectj.lang.ProceedingJoinPoint

JDK 实现

- Runnable 和 Callable

### 状态模式

基本概念

- 允许对象在内部状态发生变化时更改其行为
- 这种模式接近于有限状态机的概念
- 可以解释为策略模式 能够通过调用模式接口中定义的方法来切换模式

Spring AOP 实现

- 状态对象 - org.springframework.aop.framework.ProxyConfig

- 影响对象 - org.springframework.aop.framework.AopProxy
  - org.springframework.aop.framework.CglibAopProxy
  - org.springframework.aop.framework.JdkDynamicAopProxy

## 第五章 Spring AOP 在 Spring 内部的应用

### Spring AOP 在 Spring 事件中的使用

核心 API

- org.springframework.context.event.EventPublicationInterceptor

描述

- 当 Spring AOP 代理的 Bean 中 JoinPoint 方法执行后 Spring ApplicationContext 将发布一个自定义事件(ApplicationEvent 子类)

使用限制

- EventPublicationInterceptor 关联的 ApplicationEvent 子类必须存在单参数的构造器
- EventPublicationInterceptor 需要被声明为 Spring Bean
