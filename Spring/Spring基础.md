# 目录

[TOC]

![spring](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ea52f33c2a9a83be5c03a12.jpg)

![](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ea52f39c2a9a83be5c03ece.png)

## Spring IOC

### 什么是ioc（控制反转）

首先IOC是Inversion of Controller即控制反转 它并不是一种新的技术而是一种新的编设计思想。原来创建对象的话需要使用关键字new,需要程序主动去寻找能够让程序正常运行的类 ,从而造成了类与类之间有很强的依赖关系,代码不够灵活,修改一个类可能会涉及很多类的修改 ioc的出现把创建对象的主动权交给了第三方即ioc容器,程序需要什么对象,ioc就给你什么对象,程序从原来的主动寻找对象到现在被提供对象,原来的依赖关系就没有了,现在类与类之间都依赖于ioc容器,靠ioc容器来建立它们之间的关系

### DI（依赖注入）

DI即Dependency Injection DI其实就是在程序运行期间动态地向某个对象提供它所需要的其他对象,而Spring正是使用反射来实现注入的

**虽然通过反射创建对象的方式的效率没有使用new关键字直接创建对象高,但是降低了程序之间的耦合性,即解耦,它使得 程序之间松散耦合,这样也方便测试,利于功能复用,更重要的是使得程序的整个体系结构变得非常灵活。**

----

### IOCxml约束文档

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           https://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>    
```

### 创建工厂模式解耦

bean.properties文件

```properties
accountService = cn.qingweico.service.impl.AccountServiceImpl
accountDao = cn.qingweico.dao.impl.AccountDaoImp
```

bean.xml文件

```java
public class BeanFactoryImpl {
    private static Map<String,Object> beans;
    static  {
        try {
            //实例化properties对象
            Properties properties = new Properties();
            //获取properties文件的流对象
            InputStream inputStream = BeanFactoryImpl.class.getClassLoader().getResourceAsStream("bean.properties");
            beans = new HashMap<String,Object>();
            assert inputStream != null;
            properties.load(inputStream);
            Enumeration<Object> keys = properties.keys();
            while(keys.hasMoreElements()){
                String key  = keys.nextElement().toString();
                String beanPath = properties.getProperty(key);
                Object value = Class.forName(beanPath).getConstructor().newInstance();
                beans.put(key,value);
            }
        }catch (Exception e){
            throw  new ExceptionInInitializerError(e);
        }
        //根据map容器获取Bean对象 获取的对象是单例的
        public static Object getBean(String beanName){
            return beans.get(beanName);
        }
```

根据bean的id获取想要的类

```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean.xml");
//转换获取Bean类型的两种方式
AccountService accountService = applicationContext.getBean("accountService", AccountService.class);
//AccountService accountService = (AccountService)applicationContext.getBean("accountService");
```

ApplicationContext的三个实现类

```java
ClassPathXmlApplicationContext        //加载类路径下的配置文件
FileSystemXmlApplicationContext       //加载磁盘任意路径下的配置文件
AnnotationConfigApplicationContext    //通过注解来创建ioc容器
```

ioc核心容器的两大接口 ApplicationContext  BeanFactory

```properties
ApplicationContext在创建ioc核心容器时采用立即加载的方式,即读取完配置文件之后就会立即创建对象
BeanFactory在创建ioc核心容器时采用延迟加载的方式,即什么时候用到才会创建对象,读取完配置文件之后不会创建对象
```

### 三种创建bean对象的方式

- 使用默认构造函数创建

  ```xml
  <!--配置文件中除了id和class标签和属性外没有其他属性和标签外 默认使用无参构造函数创建bean对象.如果没有则无法创建对象-->
  <bean id="accountService"class="cn.qingweico.service.impl.AccountServiceImpl"/>
  <bean id="accountDao" class="cn.qingweico.dao.impl.AccountDaoImpl"/>
  ```

- 使用普通工厂中的方法创建对象（使用类中的方法创建对象 并存入spring容器中）

  ```java
  // 工厂类
  public class Factory {
      public AccountService getAccountService(){
          return new AccountServiceImpl();
      }
  ```

  ```xml
  <bean id ="factory" class = "cn.qingweico.factory.Factory"/>
  <bean id="accountService" factory-bean="factory" factory-method="getAccountService"/>
  ```

- 使用普通工厂中的静态方法创建对象

  ```java
  // 工厂类
  public static  AccountService getAccountService(){
  	return new AccountServiceImpl();
  }
  ```

  ```xml
  <bean id="accountService" class = "cn.qingweico.factory.Factory" factory-method="getAccountService"/>
  ```

### bean作用的范围

- singleton          单例（默认）
- prototype        多例对象
-  request            作用于web请求范围
- session             作用于web的会话范围
- global-session 作用域集群环境的会话范围即全局会话

### bean的生命周期

- 单例对象随着ioc容器的创建而创建随着容器的销毁而销毁(需要手动关闭ioc容器（使用close方法）,销毁方法才会执行)

  ```java
  ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean.xml");
  AccountService accountService = (AccountService)applicationContext.getBean("accountService");
  accountService.saveAccount();
  /*关闭后执行销毁方法*/
  applicationContext.close();
  ```

- 多例对象则是ioc容器创建时不会创建对象,当什么时候用到时才会创建对象,而当对象没有引用时则被gc回收

-----

### spring依赖注入

- 构造函数注入

  ```java
  //构造函数
  public AccountServiceImpl(String name,Date date){
      System.out.println(name + "----" + date);
  }
  ```

  ```xml
  <bean id="accountService" class="cn.qingweico.service.impl.AccountServiceImpl">
      <!-- 引用类型注入-->
      <constructor-arg name="date" ref="date"/> 
      <!-- String类型注入-->
      <constructor-arg name="name" value="姓名"/> 
    </bean>
   <bean id="date" class = "java.util.Date"/>
  </beans>
  ```

- set方法注入(需要有默认的无参构造函数)

  ```java
  private String name;
  private Date date;
  public  AccountServiceImpl(){}
      public void setName(String name) {
          this.name = name;
      }
      public void setData(Date date) {
          this.date = date;
      }
  ```

  ```xml
   <bean id="accountService" class="cn.qingweico.service.impl.AccountServiceImpl">
       <!-- String类型 -->
      <property name="name" value="姓名"/>   / 
       <!-- 引用类型 -->
      <property name="data" ref = "date"/>   // 
    </bean>
    <bean id="date" class = "java.util.Date"/>
  ```

- 复杂数据类型的注入

  ```java
  private String[] array;
  private List<String> list;
  private Set<String> set;
  private Map<String,String> map;
  private Properties properties;
  
  public void setArray(String[] arr) {
      this.array = arr;
  }
  
  public void setList(List<String> list) {
      this.list = list;
  }
  
  public void setSet(Set<String> set) {
      this.set = set;
  }
  
  public void setMap(Map<String, String> map) {
      this.map = map;
  }
  
  public void setProperties(Properties properties) {
      this.properties = properties;
  }
  ```

  ```xml
  <!--具有相同结构的数据类型标签可以互换-->
  <bean id="accountService" class="cn.qingweico.service.impl.AccountServiceImpl">
      <property name="array">
        <array>
          <value>数组</value>
        </array>
      </property>
      <property name="list">
        <list>
          <value>列表</value>
        </list>
      </property>
      <property name="set">
        <set>
          <value>set集合</value>
        </set>
      </property>
      <property name="map">
        <map>
          <entry key="001" value="map">
          </entry>
        </map>
      </property>
      <property name="properties">
        <props>
          <prop key="001">properties</prop>
        </props>
      </property>
    </bean>
  </beans>
  ```

### IOC注解

#### spring注解约束文档

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
</beans>
```

```xml
<!--开启注解扫描-->
<context:component-scan base-package="cn.qingweico"/>
<!--开启spring对注解支持-->
<context:annotation-config/>
```

- @Component 用于把当前类注入Spring容器中（衍生以下三个注解,功能和作用一摸一样）

  - @Controller    一般用在表现层
  - @Service         一般用在业务层
  - @Repository   一般用在持久层

  ```java
  /*指定ioc容器bean的唯一id 默认是类名首字母小写 value可省略*/
  @Repository(value = "accountDao")
  public class AccountDaoImpl{} 
  ```

  ```xml
  <beans id="accountDao" class="cn.qingweico.service.impl.AccountDaoImpl">
  ```

- @Autowried     按照ioc容器中的bean数据类型注入 若数据类型相同则按照变量名称注入<font style="color:purple;font-size:20px">@Autowried不可以为静态成员变量注入</font>

  ```java
  @Repository("accountDao1")
  public class AccountDaoImpl implements AccountDao {
      @Override
      public void saveAccount() {
          System.out.println("保存了账户111");
      }
  }
  ```

  ```java
  @Repository("accountDao2")
  public class AccountDaoImpl2 implements AccountDao {
      @Override
      public void saveAccount() {
          System.out.println("保存了账户222");
      }
  }
  ```

  ```java
  @Service(value ="accountService")
  public class AccountServiceImpl implements AccountService {
      // 若使用变量名称accountDao1 会输出 "保存了账户111"
      // 若使用变量名称accountDao2 会输出 "保存的账户222"
      @Autowired
      private AccountDao accountDao;
      @Override
      public void saveAccount() {
          accountDao.saveAccount();
      }
  }
  ```

  ![image-20200620211710439](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5f0ace9314195aa594182ac7.png)

  当使用`@Autowried`注入数据时,但此时容器中存在两个数据类型都为AccountDao的Bean,此时会报错,当数据类型相时,`@Autowried`会根据变量的名称来匹配相应的Bean,匹配成功则注入

- @Qualifier(value = "")   指定注入bean的id 不能单独使用,必须要与@Autowired一起使用

- @Resource(name = "")   直接注入bean的id 可以单独使用(name不能省略）

- ```properties
  以上三种注解只能注入bean类型的数据 而基本类型的数据和String等则不能使用且集合类型的数据只能通过xml来配置
  ```

- @Scope( value = "")    改变scope的作用范围（可以为prototype singleton）

- @PostConstruct         用于指定初始化方法

- @PreDestroy              用于指定销毁方法

  ```xml
  <!--部分注解所需要的依赖-->
  <dependency>
          <groupId>javax.annotation</groupId>
          <artifactId>javax.annotation-api</artifactId>
          <version>1.3.2</version>
  </dependency>
  ```

  ```java
  @PostConstruct  
  public void init(){
  System.out.println("初始化方法执行了。。。");
  }
  ```

  ```java
  @PreDestroy
  public void  destroy(){
  System.out.println("销毁方法执行了。。。");
  }
  ```

  ```xml
  <bean id="accountService" class="cn.qingweico.service.impl.AccountServiceImpl" init-method="init" destroy-method="destroy"/>
  ```


#### ioc自定义注解类

- ```java
  public class SpringConfiguration {}
  ```

- @Configuration 指定当前类是一个配置类 当该配置类作为AnnotationConfigApplicationContext对象创建的参数时 可以省略该注解

  ```java
  AnnotationConfigApplicationContext annotationConfigApplicationContext = new AnnotationConfigApplicationContext(SpringConfiguration.class);
  ```

- @PropertySource("classpath:JDBCResource.properties")    指定properties文件的位置

  - ```java
    /*可以使用注解注入数据源 spring的el表达 #{}*/
    @Value("${driverClass}")
    private String driverClass;
    @Value("${url}")
    private String url;
    @Value("${user}")
    @Value("${url}")
    private String url;
    @Value("${password}")
    private String password;
    ```

  - ```java
    @Bean(name = "jdbcTemplate") 
    @Scope("prototype")
    public JdbcTemplate createTemplate(DataSource dataSource){
        return new JdbcTemplate(dataSource);
    }
    @Bean(name = "dataSource")
    public DataSource createDataSource() {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        try {
            dataSource.setUser(user);
            dataSource.setPassword(password);
            dataSource.setJdbcUrl(url);
            dataSource.setDriverClass(driverClass);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
        return  dataSource;
    }
    ```

  - ```properties
    driverClass=com.mysql.cj.jdbc.Driver
    url=jdbc:mysql://localhost:3306/sys?useSSL=false&serverTimezone=UTC
    user=root
    password=990712
    ```

- @Import(JDBCConfiguration.class)  用于导入其他的配置类 有import的配置类是父类 而其他类都是子配置类

- @ComponentScan(basePackages = "cn.qingweico") basePackages和value的作用都是指定创建容器时所要扫描的包

- @Bean(name = "")  将当前方法的返回值存入到ioc容器中 name 表示bean的id  默认为方法名

### Spring整合junit

```xml
<!-- 导入依赖 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.2.RELEASE</version>
    <scope>test</scope>
</dependency>
```

```java
@RunWith(SpringJUnit4ClassRunner.class)                     //开启junit对spring的支持
```

```java
@ContextConfiguration(classes = SpringConfiguration.class)  //基于注解的junit整合
```

```java
@ContextConfiguration(locations = "classpath:bean.xml")     //基于xml的junit整合
```

---

## Spring AOP

### 事务控制

自定义工具类ConnectionUtils  TransactionManager实现事务控制

```java
// 连接的工具类  实现从当前数据源中获取一个连接 并实现和当前线程的绑定
public class ConnectionUtils {
    private ThreadLocal<Connection> tl = new ThreadLocal<Connection>();
    private DataSource dataSource;
    public void setDataSource(DataSource dataSource){
        this.dataSource = dataSource;
    }
    // 获取当前线程上的连接
    public Connection getThreadConnection(){
        Connection connection = tl.get();
         try {
             if(connection == null){
                 connection = dataSource.getConnection();
                 tl.set(connection);
             }
         }catch (SQLException e){
             throw  new RuntimeException(e);
         }
         return connection;
    }
    // 将连接和线程解绑*
    public void removeConnection(){
        tl.remove();
    }
```

```java
/*和事务管理相关的工具类 它实现了开始事务 提交事务 回滚事务 释放连接 */
public class TransactionManager {
    private ConnectionUtils connectionUtils;
    public void setConnectionUtils(ConnectionUtils connectionUtils){
        this.connectionUtils = connectionUtils;
    }
    /**
     * 开始事务
     */
    @Pointcut("execution(* cn.qingweico.service.impl.*.*(..))")
    public void pointcut(){}
    public void begin(){
      try {
          connectionUtils.getThreadConnection().setAutoCommit(false);
      }catch (Exception e){
          e.printStackTrace();
      }
    }
    /**
     * 提交事务
     */

    public void commit(){
       try {
           connectionUtils.getThreadConnection().commit();
       }catch (Exception e){
           e.printStackTrace();
       }
    }
    /**
     * 回滚事务
     */

    public  void rollback(){
        try {
            connectionUtils.getThreadConnection().rollback();
        }catch (Exception e){
            e.printStackTrace();
        }
    }
    /**
     * 释放连接
     */

    public void release(){
     try {
         connectionUtils.getThreadConnection().close();
         connectionUtils.removeConnection();
     }catch (Exception e){
         e.printStackTrace();
     }
    }
```

### 动态代理

特点:字节码随用随创建,随用随加载

作用:在不修改源码的基础上对已有方法进行增强

- 基于接口的动态代理

  ```java
  /*
  被代理类至少实现一个接口
  使用Proxy类中的newIProxyInstance方法
  newProxyInstance方法的三个参数是 
  ClassLoader 类加载器
  Class[]   字节码数组
  InvocationHandle 用于提供增强的代码
  */
  public class Producer implements IProducer {
      public static void main(final String[] args) {
           final Producer producer = new Producer();
           IProducer iProducer =               
           (IProducer)Proxy.newProxyInstance(producer.getClass().getClassLoader(), producer.getClass().getInterfaces(),
                         //匿名内部类访问外部变量时 其要被final关键字修饰
                         new InvocationHandler() {
                             /**
                       * 作用 ：执行被代理对象的任何接口方法都会经过该方法
                       * @param o           代理对象的引用
                       * @param method      当前执行的方法
                       * @param objects     当前执行方法所需要的参数
                       * @return            和被代理对象有相同的返回值
                       * @throws Throwable .
                       */
      public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
          Object returnValue = null;
          Float money = (Float)objects[0];
          if("saleProduct".equals(method.getName())){
              returnValue =  method.invoke(producer,money*0.8f);
          }
          return returnValue;
      }
  });//返回Object类型
  iProducer.saleProduct(10000f); //对Iproducer接口中saleProduct方法进行增强
      }
  }
  ```

- 基于子类的动态代理

  ```java
  /*
  被代理类不能是最终类
  使用Enhancer类中的create方法
  create方法的参数
     Class: 用于指定被代理对象的字节码
     CallBack: 用于提供增强的代码
               使用该接口的子接口实现类 MethodInterceptor
  */
  public class Producer {  //基于子类的动态代理不用实现接口
      public static void main(String[] args) {
          final Producer producer = new Producer();
          Producer cglibProduce = (Producer) Enhancer.create(producer.getClass(), new MethodInterceptor() {
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                Object returnValue = null;
                Float money = (Float)objects[0];
                if("saleProduct".equals(method.getName())){
                    returnValue =  method.invoke(producer,money*0.8f);
                }
                return returnValue;
            }
        });
      cglibProduce.saleProduct(12000f); //对Produce类中saleProduct方法进行增强
  }
  ```

### 基于动态代理实现事务控制

```java
//用于创建Service的代理对象的工厂
public class BeanFactory {
    private AccountService accountService;
    public final void setAccountService(AccountService accountService){
        this.accountService = accountService;
    }
    private TransactionManager transactionManager;
        public void setTransactionManager(TransactionManager transactionManager){
        this.transactionManager = transactionManager;
    }
public AccountService getAccountService(){
return (AccountService)Proxy.newProxyInstance(accountService.getClass().getClassLoader(),
                accountService.getClass().getInterfaces(),
                new InvocationHandler() {
                    public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
                        Object returnValue;
                        try {
                            transactionManager.begin();   //开始事务
                            //对AccountService接口下所有的方法都会进行增强
                            returnValue = method.invoke(accountService,objects);
                            transactionManager.commit();  //提交事务
                            return returnValue;
                        }catch (Exception e){
                            transactionManager.rollback(); //回滚事务
                            throw new RuntimeException(e);
                        }finally {
                            transactionManager.release();  //释放连接
                        }
                }
                });
}
```

### AOP (Aspect Oriented Programmer 面向切面编程)

作用: ***将程序中重复的代码抽取出来,在需要执行的时候使用动态代理技术,对在不修改源码的基础上对已有方法进行增强***

优势：

- 减少重复代码
- 提高开发效率
- 维护方便

```properties
 AOP相关术语
       JoinPoint 连接点
           所谓连接点是指那些被拦截的点 在spring中 这些点指定是方法 因为spring只支持方法类型的连接点
        PointCut  切入点
            所谓切入点是指我们要对哪些JoinPoint进行拦截的点
        通知 : 前置通知  后置通知 异常通知 最终通知 环绕通知
        织入 Weaving  是指把增强应用到目标对象来创建新的代理对象的过程（返回代理对象并加入事务支持）
        Aspect 切面 就是通知和切入点的结合
```

---

### AOP约束（包括xml和注解以及aop）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           https://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/aop
                           https://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/context
                           https://www.springframework.org/schema/context/spring-context.xsd">
</beans>    
```

### Spring中基于xml的AOP配置实现事务控制

- 把通知Bean交给Spring来管理

- 使用aop:congfig标签来表明配置的开始

  ```xml
  <aop:config></aop:config>
  ```

- 使用aop:aspect标签表明配置切面

  - id 给切面提供一个唯一标志
  - ref 指定通知类bean的id

- 在aop:aspect标签内部使用对应的标签来配置通知的类型

  - aop:before 表明配置前置通知
  - aop:after-returning 表明配置后置通知
  - aop:after-throwing 表明是异常通知
  - aop:after 表明最终通知
  - method 用于指定类中的那个方法是前置通知
  - pointcut 用于指定切入点表达式 该表达式的含义是对业务层的哪些方法进行增强

***切入表达式的写法***

execution(表达式)

访问修饰符  返回值 包名.包名...类名.方法名（参数列表）

```properties
通配符
    访问修饰符可以省略
    返回值可以使用* 代表返回任意类型
    包名可以使用* 代表任意包 有几级包就写几个*  其中*.. 代表当前包及其子包
    类名和方法名都可以使用*  代表任意类和方法
    参数   基本类型就直接写名称
    引用类型写全限定名称
    可以使用*表示任意类型的参数（前提有参数）
    可以使用.. 表示有无参数均可 有参数可以是任意类型
    开发中切入表达式通常的写法 ：切入业务层下实现类中所有的方法
    * cn.qingweico.aop.Impl.*.*(..)
```

```xml
<!--通知类--> 
<bean id="transactionManager" class="cn.qingweico.utils.TransactionManager" >
    <property name="connectionUtils" ref="connectionUtils"/>
</bean> 
<aop:config>
    <!--配置通用切入点表达式-->
    <!--pointcut属性当在aspect内部使用时只能当前切面使用 而在外部时 所有切面均可用-->
    <aop:pointcut id="pointcut" expression="execution(* cn.qingweico.service.impl.*.*(..))"/>
    <!--配置切面-->
    <aop:aspect id="transactionManager" ref="transactionManager">
        <!--配置前置通知  开始事物-->
        <!--前置通知 : 在切入点方法执行之前执行-->
        <aop:before method="begin" pointcut-ref="pointcut"/>
        <!--配置后置通知 提交事物-->
        <!--后置通知 ：在切入点方法正常执行之后执行-->
        <aop:after-returning method="commit"  pointcut-ref="pointcut"/>
        <!--配置异常通知  回滚事物-->
        <!--异常通知 在切入点方法产生异常之后执行-->
        <aop:after-throwing method="rollback" pointcut-ref="pointcut"/>
        <!--配置最终通知  释放连接-->
        <!--最终通知：无论切入点方法是否正常执行都会在其后面执行-->
        <aop:after method="release" pointcut-ref="pointcut"/>
        <!--环绕通知-->
        <aop:around method="aroundLogger" pointcut-ref="pointcut"/>
    </aop:aspect>
</aop:config>
```

#### spring中的环绕通知

```xml
<!--导入所需要的依赖-->  
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.2</version>
</dependency>
```

环绕通知spring框架为我们提供的一种可以在代码中手动控制增强方法何时执行的方式

```java
public Object aroundLogger(ProceedingJoinPoint proceedingJoinPoint){
    Object returnValue = null;
    try {
        Object[] args = proceedingJoinPoint.getArgs(); //得到方法执行所需的参数
        System.out.println("环绕通知打印了日志--前置");
        returnValue = proceedingJoinPoint.proceed();   //明确调用业务层的方法（切入点方法）
        System.out.println("环绕通知打印了日志--后置");
        return returnValue;
    }catch(Throwable t){
        System.out.println("环绕通知打印了日志--异常");
        throw  new RuntimeException(t);
    }finally {
        System.out.println("环绕通知打印了日志--最终");
    }
}
```

### Spring中基于注解AOP配置

```xml
<!--开启注解扫描-->
<context:component-scan base-package="cn.qingweico"/>
<!--开启Spring对AOP注解的支持-->
<aop:aspectj-autoproxy/>
```

- @Aspect  表明当前类是一个切面类

- ```java
  /*配置切入点表达式*/
  @Pointcut("execution(* cn.qingweico.service.impl.*.*(..))")
      private void pointcut(){
   }
  ```

- ```java
  @Before("pointcut()")              //配置前置通知
  ```

- ```java
  @AfterReturning("pointcut()")      //配置后置通知
  ```

- ```java
  @AfterThrowing("pointcut()")       //配置异常通知
  ```

- ```java
  @After("pointcut()")               //配置最终通知
  ```

- ```java
  @Around("pointcut()")              //配置环绕通知
  ```

- 基于注解的四个通知其最终通知会在后置通知之前执行 而使用环绕通知则不会出现这样的问题

- ```java
  /*使用注解时当使用四个通知时则实现不了事务的控制 此时应该使用环绕通知才能实现事务的控制*/
  @Around("pointcut()")
  public Object around(ProceedingJoinPoint proceedingJoinPoint){
      Object returnValue = null;
      try {
          Object[] args = proceedingJoinPoint.getArgs();
          this.begin();
          returnValue = proceedingJoinPoint.proceed();
          this.commit();
      }catch(Throwable t){  //使用Throwable来拦截异常
          this.rollback();
          throw new RuntimeException(t);
      }finally{
          this.release();
      }
      return returnValue;
  }
  ```

### jdbcTemplate

```xml
<!--导入jdbc依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.2.RELEASE</version>
</dependency>
```

jdbcTemplate的crud语句

```xml
<!--配置jdbcTemplatebean文件时应该将数据源作为函数参数传入-->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
</bean>
```

```java
ApplicationContext applicationContext = new  ClassPathXmlApplicationContext("template.xml");
JdbcTemplate jt= applicationContext.getBean(JdbcTemplate.class);
// cud语句都使用update语句
// args是用来替换sql语句占位符?的参数 
jt.update(String sql, Object...args);
// 查询语句
// 返回多行
// T为返回参数的类型 args是用来替换sql语句占位符?的参数
List<T> res = jt.query(String sql,new BeanPropertyRowMapper<T>(Class<?> T),...args);
// 返回一行一列
jt.queryForObject(String sql,resultType<T>,...args);
// 查询所有
 List<Map<String,Object>> accounts = jt.queryForList("select * from account");
```

#### jdbcTemplate继承父类jdbcDaoSupport实现Dao层减少重复代码 只能在基于XML配置时使用

```java
public class AccountDaoImpl extends JdbcDaoSupport implements AccountDao {
    public void findByAll() {
        assert getJdbcTemplate() != null;
        List<T> list = getJdbcTemplate().query(String sql, new BeanPropertyRowMapper<T>(Class<?> T));     
   }
}
/*AccountDaoImpl继承了jdbcDaoSupport类 而父类中有setDataSorce方法 
所以在为子类创建Bean对象时应该使用set方法DataSource注入依赖*/
```

### Spring中基于xml的声明式事务控制(Sping自带的事务控制)

#### 声明式事务控制的约束

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:tx="http://www.springframework.org/schema/tx"
        xsi:schemaLocation=" http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd">
```

#### 导入依赖

```xml
 <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>5.2.2.RELEASE</version>
</dependency>
```

```xml
<!--配置事务管理器 spring提供-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
</bean> 
```

```xml
<!--配置事务的通知-->
<!--id                   给事务通知起一个唯一标志
    transaction-manager  给事务通知提供一个事务管理器引用-->
<tx:advice id="tx" transaction-manager="transactionManager">
      <tx:attributes>
          <tx:method name="*" propagation="REQUIRED" read-only="false"/>
          <tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
      </tx:attributes>
 </tx:advice>      
```

```xml
<!--配置aop中的切入点表达式-->
<aop:config>
   <aop:pointcut id="pointcut" expression="execution(* cn.qingweico.service.impl.*.*(..))"/>
<!--建立事务通知和切入点表达式之间的对应关系-->
    <aop:advisor advice-ref="tx" pointcut-ref="pointcut"/>
</aop:config>
```

#### 配置事务的属性

- 在事务通知aop:advice标签的内部
- isolation: 用于指定事务的隔离级别 默认是default 式数据库默认的隔离级别
- propagation: 用于指定事务的传播行为 默认是required 表示一定有事务的增删改 如果查询的话使用supports
- read-only; 用于指定事务是否只读 只有查询方法才能设置为true 默认值为false
- timeout: 用于事务的超时时间 默认是-1 表示永不超时 如果指定了单位 则以秒为单位
- rollback-for: 用于指定一个异常 当产生该异常时 事务回滚 产生其他异常时 事务不会回滚 如果没有默认值 表示任何异常都回滚
- no-rollback-for: 与rollback-for相反  如果没有默认值 表示任何异常都回滚

### Spring中基于注解的声明式事务控制(Sping自带的事务控制)

```xml
<!-- 开启注解扫描 -->
<context:component-scan base-package="cn.qingweico"/>
<!-- 开启spring对注解事务的支持 -->  
<tx:annotation-driven transaction-manager="transactionManager"/>
```

@Transactional 对需要事务支持的类使用该注解(该注解也可以标注在类上)

````java
// 可以对单个查询方法进行配置
@Transactional(propagation =  Propagation.SUPPORTS,readOnly = true)
````

```xml
<bean id="template" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
</bean>
```

### Spring中使用纯注解的声明式事务控制

```java
@EnableTransactionManagement  // 开启spring对注解的支持
```

```java
// 用于创建事务管理器对象并加入ioc容器中
@Bean(name = "transactionManager")
public PlatformTransactionManager createTransactionManager(DataSource dataSource){
    return new DataSourceTransactionManager(dataSource);
}
```
