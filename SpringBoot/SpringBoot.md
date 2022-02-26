# 目录

[TOC]

# SpringBoot（v2.3.2.RELEASE）

## SpringBoot配置

### 场景启动器

spring-boot-start

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.0.RELEASE</version>
</parent>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

### @SpringBootApplication注解

#### @SpringBootConfiguration

- @Configuration

#### @EnableAutoConfiguration

- @AutoConfigurationPackage

  - @Import(AutoConfigurationPackages.Registrar.class)(Spring底层注解 给容器导入一个组件)

    将主配置类(@SpringBootApplication标注的类)所在的包及其下所有的子包里面所有的组件扫描到容器中,或者使用@SpringBootApplication(scanBasePackages = {"com.example"})指定扫描的包,也可以使用注解@ComponentScan(value = "com.example")指定扫描的包

- @Import({AutoConfigurationImportSelector.class})

  - AutoConfigurationImportSelector 将需要导入的组件以全类名的方式返回并添加到容器中,即给容器中导入很多的自动配置类

```java
/*AutoConfigurationImportSelector.class*/
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

SpringBoot在启动时从类路径下<font style="color:purple;font-size:20px">spring-boot-autoconfigure-2.3.0.RELEASE.jar/META-INF/spring.factories</font>找到EnabledAutoConfiguration指定的值并将这些值作为配置类导入到容器中

### SpringBoot全局配置文件

- Application.properties
- Application.yml

#### yml文件

将空格玩到极致的语法  所有的冒号后面都有一个空格

```yaml
person:          #person对象的写法 行内写法 person: {name: jack,age: 21}
  name: jack     #字符串默认不加引号 单引号表示转义特殊字符 双引号则不转义特殊字符
  age: 21
  list:          #数组类型 行内写法 list: [hello, world]
    - hello
    - world
  maps:          #map集合类型 和对象类型一样 key: value 键和值
    k1: v1
    k2: 12
```

```properties
#等价于properties文件
person.name=jack
person.age=21
person.list=hello,world
person.maps.k1=v1
person.maps.k2=12
```

#### 读取yml文件

```java
package com.example.entry;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
import java.util.Arrays;
import java.util.Map; 
@Component                   
@ConfigurationProperties(prefix = "person") /*匹配yml文件中以person开头的配置文件*/
public class Person {
    private String name;
    private Integer age;
    private String[] list;
    private Map<String,Object> maps;
    public Map<String, Object> getMaps() {
        return maps;
    }

    public void setMaps(Map<String, Object> maps) {
        this.maps = maps;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String[] getList() {
        return list;
    }

    public void setList(String[] list) {
        this.list = list;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", list=" + Arrays.toString(list) +
                ", maps=" + maps +
                '}';
    }
}
```

#### @ConfigurationProperties和@Value的区别

@ConfigurationProperties默读取全局配置文件

相同点都是可以从配置文件中读取值并注入到字段中(前提是字段所属的类一定要在ioc容器中)

@ConfigurationProperties批量注入配置文件中的数据,而@Value则是一次一次注入

前者支持松散绑定语法(下划线转驼峰),而后者不支持

前者不支持SPEL,后者支持

前者支持数据校验,后者不支持

前者支持复杂数据类型的封装,后者不支持

前者支持注入静态类型的变量,后者不支持(当使用set方法时也可以完成为静态变量注入,注意set方法不可以是静态的, 且@Value注解加在set方法上)

### PropertySouce和ImportResouce注解的区别

#### PropertySource

```java
@PropertySouce(value = {"classpath:xxx.properties"}) //加载指定的配置文件
```

#### ImportResource（标注在一个配置类上）

```java
@ImportResource(locations = {"classpath:bean.xml"}) //导入spring的配置文件(xxx.xml)   
```

SpringBoot不能自动识别自定义的配置文件,需要手动导入

SpringBoot不推荐用此方式向Spring容器中添加组件,可以自定义一个配置类代替配置文件,使用`@Bean`注解方式向容器中加入组件

### 配置文件占位符

```properties
person.name=jack${person.hello:hello} #不存在的属性直接看成字符串 可以赋予默认值
person.age=${random.int}              #随机数
person.list=hello,world
person.maps.k1=v1
person.maps.k2=12_${person.name}      #配置文件存在的属性则替换
```

### 多ProFile文件

#### properties文件

在主配置文件中激活环境

```properties
spring.Profiles.active = dev
```

application-dev.properties

```properties
server.port = 8082
```

#### yml文件

使用文档块的形式

```yml
server:
  port: 8080
spring:
  profiles:
    active: dev  # 不写激活默认环境（8080）
---              # ---代表一个文档块
server:
  port: 8081
spring:
  profiles: dev  # 开发环境
---
server:
  port: 8082
spring:
  profiles: prod # 生产环境
```

#### 使用命令行参数

```java
--spring.profiles.active=dev
```

#### 使用虚拟机参数

```java
-Dspring.profiles.active=dev
```

### 配置文件的加载顺序

优先级为：项目根路径下的config中的配置文件 > 项目根路径下配置文件 > 类路径下config中的配置文件 > 类路径下配置文件

```properties
/config/xxx.properties
/xxx.properties
classpath:/config/xxx.properties
classpaht:/xxx.properties
```

SpringBoot会全部加载这四个位置的配置文件,高优先级的配置文件会覆盖优先级低的相同配置文件

```properties
# 等号之间不要加空格
# 在项目打包时可以加载指定路径的配置文件
--spring.config.location=xxx.properties
```

```properties
# 等号之间不要加空格
java -jar hello-0.0.1-SNAPSHOT.jar --spring.config.location=E:/application.properties
```

```java
server.servlet.context-path=/test
```

### 外部配置加载顺序

- 命令行

```properties
#运行jar文件时指定端口号 运行路径,等号之间不要加空格 多个配置空额隔开
java -jar hello-0.0.1-SNAPSHOT.jar --server.port=8848 --server.servlet.context-path=/test
```

- 优先加载带profile的文件,再加载不带profile的文件
- 从jar包外向内加载

### SpringBoot访问静态资源

```properties
classpath:/META-INF/resources/
classpath:/resources/
classpath:/public/
classpath:/static/
/:
```

```yaml

spring:
  mvc:
  # 配置文件 自定义静态资源的f
    static-path-pattern: /static/**
  resources:
  # 配置文件 自定义静态资源的路径
    static-locations: classpath:/hello,classpath:/test
```

#### 关于Spring Boot中使用request.getServletContext().getRealPath路径获取问题

`String path = req.getServletContext().getRealPath("");`

默认情况下返回的是一个临时文件夹的地址

```java
package org.springframework.boot.web.servlet.server;	
class DocumentRoot{
    private static final String[] COMMON_DOC_ROOTS = { "src/main/webapp", "public", "static" };
}
```

所以只要在项目根目录新建文件夹public或者static或者在类路径下新建文件夹webapp即可读到路径

优先级 webapp > public > static(同时存在)

### Thymeleaf

```html
<!--引入名称空间-->
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

#### 抽取公共代码片段

```html
<!--footer模板-->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
    <body>
        <!--自定义公共片段名称-->
        <div th:fragment="copy">
            &copy; 2011 The Good Thymes Virtual Grocery
        </div>
    </body>
</html>
```

#### 引入公共代码片段

```html
<body>
...
    <div th:insert="footer :: copy"></div>
</body>
 
~{templatename::fragmentname} 
~{模板名::代码片段名}
~{templatename::selector}
~{模板名::选择器}
```

#### 三种引入公共代码片段的方式

- `th:insert`
- `th:replace`
- `th:include`

```html
<footer th:fragment="copy">
    &copy; 2011 The Good Thymes Virtual Grocery
</footer>
```

```html
<body>
...
    <div th:insert="footer :: copy"></div>
    <div th:replace="footer :: copy"></div>
    <div th:include="footer :: copy"></div>
</body>
```

```html
<body>
...<!--th:insert将整个片段直接插入到指定标签中-->
    <div>
        <footer>
            &copy; 2011 The Good Thymes Virtual Grocery
        </footer>
    .<!--th:replace将整个片段替换原来的标签-->
    </div>
    <footer>
        &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
    .<!--th:include将片段的内容引入到指定标签中-->
    <div>
        &copy; 2011 The Good Thymes Virtual Grocery
    </div>
</body
```

### 添加视图映射和拦截器

```java
package com.example.config;
import com.example.component.LoginHandleInterceptor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
@Configuration
public class MyMVCConfiguration implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("login");

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandleInterceptor()).addPathPatterns("/**").excludePathPatterns("/", "/login");
    }
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/**").addResourceLocations(
                "classpath:/templates/",
                "classpath:/static/",
                "classpath:/resources/");
    }
}
```

```java
package com.example.component;
import org.springframework.web.servlet.HandlerInterceptor;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
public class LoginHandleInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
       Object LoginSession = request.getSession().getAttribute("LoginSession");
       if(LoginSession == null){
           response.sendRedirect("/");
           return false;
       }
       else return true;
    }
}
```

### 错误处理机制

####  ErrorMvcAutoConfiguration

- ErrorPageCustomizer

```java
/*错误产生时发出/error请求 如果配置文件中取不到error.path的值则使用/error请求*/
class ErrorProperties
    @Value("${error.path:/error}")
    private String path = "/error";
```

- BasicErrorController

```java
/*处理/error的控制器*/
@Controller
@RequestMapping({"${server.error.path:${error.path:/error}}"})
class BasicErrorController{
    /*处理text/html请求类型的错误*/
    @RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
    public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
        HttpStatus status = getStatus(request);
        Map<String, Object> model = Collections
            .unmodifiableMap(getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.TEXT_HTML)));
        response.setStatus(status.value());
        ModelAndView modelAndView = resolveErrorView(request, response, status, model);
        return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
    }
   /*处理其他客户端的错误*/
    @RequestMapping
    public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
        HttpStatus status = getStatus(request);
        if (status == HttpStatus.NO_CONTENT) {
            return new ResponseEntity<>(status);
        }
        Map<String, Object> body = getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.ALL));
        return new ResponseEntity<>(body, status);
    }
}
```

```java
protected ModelAndView resolveErrorView(HttpServletRequest request, HttpServletResponse response, HttpStatus status,Map<String, Object> model) {
    for (ErrorViewResolver resolver : this.errorViewResolvers) {
        ModelAndView modelAndView = resolver.resolveErrorView(request, status, model);
        if (modelAndView != null) {
            return modelAndView;
        }
    }
    return null;
}
```

```java
@FunctionalInterface
public interface ErrorViewResolver {

	/**
	 * Resolve an error view for the specified details.
	 * @param request the source request
	 * @param status the http status of the error
	 * @param model the suggested model to be used with the view
	 * @return a resolved {@link ModelAndView} or {@code null}
	 */
	ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model);

}
```

- DefaultErrorViewResolver

```java
/*对错误请求进行视图解析*/
public class DefaultErrorViewResolver implements ErrorViewResolver, Ordered {
public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
    ModelAndView modelAndView = this.resolve(String.valueOf(status.value()), model);
    if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
        modelAndView = this.resolve((String)SERIES_VIEWS.get(status.series()), model);
    }

    return modelAndView;
}
private ModelAndView resolve(String viewName, Map<String, Object> model) {
    /*默认去error路径下找404页面*/
    String errorViewName = "error/" + viewName;
    /*可以使用模板引擎解析页面*/
    TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName, this.applicationContext);
    /*模板引擎可以使用返回指定的ModelAndView 否则使用resolverResouce方法*/
    return provider != null ? new ModelAndView(errorViewName, model) : this.resolveResource(errorViewName, model);
}
private ModelAndView resolveResource(String viewName, Map<String, Object> model) {
    String[] var3 = this.resourceProperties.getStaticLocations();
    int var4 = var3.length;

    for(int var5 = 0; var5 < var4; ++var5) {
        String location = var3[var5];

        try {
            Resource resource = this.applicationContext.getResource(location);
            /*在静态资源文件夹下找error/404.html页面*/
            resource = resource.createRelative(viewName + ".html");
            if (resource.exists()) {
                return new ModelAndView(new DefaultErrorViewResolver.HtmlResourceView(resource), model);
            }
        } catch (Exception var8) {
        }
    }

    return null;
}
}
```

- DefaultErrorAttributes

```java
/*获取错误数据属性*/
public class DefaultErrorAttributes implements ErrorAttributes {
    @Deprecated
    public Map<String, Object> getErrorAttributes(ServerRequest request, boolean includeStackTrace) {
        Map<String, Object> errorAttributes = new LinkedHashMap();
        errorAttributes.put("timestamp", new Date());
        errorAttributes.put("path", request.path());
        Throwable error = this.getError(request);
        MergedAnnotation<ResponseStatus> responseStatusAnnotation = MergedAnnotations.from(error.getClass(), SearchStrategy.TYPE_HIERARCHY).get(ResponseStatus.class);
        HttpStatus errorStatus = this.determineHttpStatus(error, responseStatusAnnotation);
        errorAttributes.put("status", errorStatus.value());
        errorAttributes.put("error", errorStatus.getReasonPhrase());
        errorAttributes.put("message", this.determineMessage(error, responseStatusAnnotation));
        errorAttributes.put("requestId", request.exchange().getRequest().getId());
        this.handleException(errorAttributes, this.determineException(error), includeStackTrace);
        return errorAttributes;
    }
}
```

有模板引擎的情况下会找error文件夹下的状态码.html

```html
<!--404.html-->
<!DOCTYPE html>
<html lang="en"  xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>[[${status}]]</h1>
    <h1>[[${timestamp}]]</h1>
    <h1>[[${path}]]</h1>
    <h1>[[${error}]]</h1>
    <h1>[[${message}]]</h1>
    <h1>[[${requestId}]]</h1>
</body>
</html>
```

没有模板引擎的情况下会从静态文件夹下找error/状态码.html

### 配置外部Servlet容器

#### 全局配置文件

```properties
server.port=8888
```

```java
/*修改和server有关的配置*/
@ConfigurationProperties(prefix = "server",ignoreUnknownFields = true)
class ServerProperties
```

### 注册Servlet三大组件

#### Servlet

```java
ServletRegistrationBean
```

#### Filter

```java
FilterRegistrationBean
```

#### Listener

```java
ServletListenerRegistrationBean
```

```java
package com.example.config;
import com.example.component.MyFilter;
import com.example.component.MyListener;
import com.example.component.MyServlet;
import org.springframework.boot.web.server.ConfigurableWebServerFactory;
import org.springframework.boot.web.server.WebServerFactoryCustomizer;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletListenerRegistrationBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.Collections;
@Configuration
public class MyServletConfig {
    /*配置tomcat容器端口号*/
    @Bean
    public WebServerFactoryCustomizer<ConfigurableWebServerFactory> webServerFactoryCustomizer(){
        return new WebServerFactoryCustomizer<ConfigurableWebServerFactory>() {
            @Override
            public void customize(ConfigurableWebServerFactory factory) {
                factory.setPort(8888);
            }
        };
    }
    /*注册Servlet*/
    @Bean
    public ServletRegistrationBean<MyServlet> servletRegistrationBean() {
        return new ServletRegistrationBean<>(new MyServlet(), "/myServlet/*");
    }
    /*注册Filter*/
    @Bean
    public FilterRegistrationBean<MyFilter> filterFilterRegistrationBean() {
        FilterRegistrationBean<MyFilter> filterRegistrationBean = new FilterRegistrationBean<>();
        filterRegistrationBean.setFilter(new MyFilter());
        filterRegistrationBean.setUrlPatterns(Collections.singletonList("/myServlet"));
        return filterRegistrationBean;
    }
    /*注册Listener*/
    @Bean
    public ServletListenerRegistrationBean<MyListener> servletListenerRegistrationBean() {
        return new ServletListenerRegistrationBean<>(new MyListener());
    }
}
```

#### myFilter

```java
package com.example.component;
import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
public class MyFilter extends HttpFilter {
    @Override
    protected void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("Filter had run!");
        chain.doFilter(request, response);
    }
}
```

#### myListener

```java
package com.example.component;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
public class MyListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("the project is running");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("the project is destroy");
    }
}
```

### 配置其他web容器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!--排除掉默认的tomcat容器-->
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!--再引入需要的容器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

### 配置跨域请求

```java
package com.example.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CorsConfig implements WebMvcConfigurer {
    /**
     * 页面跨域访问Controller过滤
     */
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        WebMvcConfigurer.super.addCorsMappings(registry);
        registry.addMapping("/**")
                .allowedHeaders("*")
                .allowedMethods("POST", "GET", "DELETE", "PUT")
                .allowedOrigins("*");
    }
}
```



## 数据访问

### SpringBoot JDBC自动配置原理

org.springframework.boot.autoconfigure.jdbc

- **DataSourceConfiguration** 

```properties
配置各种数据源,也可以自定义数据源
在配置文件中指定spring.datasouce.type=xxx 指定自定义数据源
```

- **DataSourceAutoConfiguration**

  - ```java
    @Import({DataSourcePoolMetadataProvidersConfiguration.class, DataSourceInitializationConfiguration.class})
    public class DataSourceAutoConfiguration {}
    ```

  - ```java
    @Import({DataSourceInitializerInvoker.class, DataSourceInitializationConfiguration.Registrar.class})
    class DataSourceInitializationConfiguration {DataSourceInitializationConfiguration() {}
    ```

  - ```java
    class DataSourceInitializerInvoker implements ApplicationListener<DataSourceSchemaCreatedEvent>, InitializingBean {
        private DataSourceInitializer getDataSourceInitializer()
    }
    ```

  - ```java
    class DataSourceInitializer {
        /*执行建表语句脚本 默认是schema-all.sql*/
        private List<Resource> getScripts(String propertyName, List<String> resources, String fallback) {
            if (resources != null) {
                return this.getResources(propertyName, resources, true);
            } else {
                String platform = this.properties.getPlatform();
                List<String> fallbackResources = new ArrayList();
                /*从resources类路径下加载脚本文件*/
                fallbackResources.add("classpath*:" + fallback + "-" + platform + ".sql");
                fallbackResources.add("classpath*:" + fallback + ".sql");
                return this.getResources(propertyName, fallbackResources, false);
            }
        }
    }
    ```

  - **自定义脚本文件**

    ```yaml
    spring:
      datasource:
        url: jdbc:mysql://localhost:3306/test?useUnicode=true&serverTimezone=UTC
        username: root
        password: 123456
        driver-class-name: com.mysql.cj.jdbc.Driver
        initialization-mode: always       #2.x之后的SpringBoot一定要加
        # SpringBoot启动时执行该建表语句
        schema:
         - classpath:student_info.sql
    ```

### SpringBoot整合Spring Data JPA

JPA (java persistence api)

#### pom.xml

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.0.RELEASE</version>
</parent>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.19</version>
    </dependency>
</dependencies>
```

#### application.yml

```yaml
spring:
    datasource:
    url: jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
jpa:
    show-sql: true
    properties:
        hibernate: true
```

#### 实体类

```java
package cn.qingweico.entity;
import lombok.Data;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
@Data
/*@Entity指明这是一个和数据库表字段相互映射的实体类*/
@Entity
/*@Table指明映射的数据库表名 默认是实体类名小写*/
@Table(name = "student")
public class Student {
    /*表明这是和数据库表主键字段映射的属性*/
    @Id
    /*主键由数据库自动生成（主要是自动增长型）*/
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    /*指定和该属性映射的数据库表中字段名称 默认是该属性名称*/
    @Column(name = "id")
    private  Integer id;
    private String name;
    private String number;
}
```

#### dao

```java
package cn.qingweico.dao;
import cn.qingweico.entity.Student;
import org.springframework.data.jpa.repository.JpaRepository;
/*JpaRepository中泛型前者是操作的实体类后者是数据库表中的主键的类型*/
public interface StudentDao extends JpaRepository<Student,Integer> {
}
```

### SpringBoot整合Mybatis

#### pom.xml

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.0.RELEASE</version>
</parent>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>1.3.2</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.19</version>
    </dependency>
</dependencies>
```

#### application.yml

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&serverTimezone=UTC
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
mybatis:
  mapper-locations: classpath:/mappers/*.xml        #配置mapper映射文件路径
  config-locations: classpath: /mybatis-config.xml  #配置mybatis全局配置文件路径
  type-aliases-package: cn.qingweico.entity         #配置包别名
server:
  port: 8848
#MyBatis注解和xml配置可以一起使用
```

#### 启动类

```java
@MapperScan("cn.qingweico.dao") /*将mapper接口扫进ioc容器中*/
```

### Druid（主从复制）

#### pom.xml

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.10</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
```

#### application.yml

```yml
spring:
  datasource:
    masterUrl: jdbc:mysql://127.0.0.1:3306/tmall?useUnicode=true&serverTimezone=UTC
    slaveUrl: jdbc:mysql://xxx:3306/tmall?useUnicode=true&serverTimezone=UTC
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    initialization-mode: always
    #数据源其他配置

    # 初始化连接池的大小
    initialSize: 5
    # 连接池中的最小空闲连接数
    minIdle: 5
    # 连接池中的最大的连接数
    maxActive: 20
    # 配置获取连接等待超时的时间
    maxWait: 60000
    # 配置获取连接等待超时的时间
    timeBetweenEvictionRunsMillis: 60000
    # 配置一个连接在池中最小生存的时间,单位是毫秒
    minEvictableIdleTimeMillis: 300000
    # 验证数据库连接的查询语句
    validationQuery: SELECT 1 FROM DUAL
    # 获取连接的时候测试连接的有效性
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    # 是否缓存preparedStatement,
    poolPreparedStatements: true
    # 配置监控统计拦截的filters,去掉后监控界面sql无法统计,'wall'用于防火墙
    filters: stat,wall,log4j2
    maxPoolPreparedStatementPerConnectionSize: 20
    # 合并多个DruidDataSource的监控数据
    useGlobalDataSourceStat: true
    # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
    #设置数据源
    type: com.alibaba.druid.pool.DruidDataSource
```

#### DruidConfig.java

```java
package cn.qingweico.config;

import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

/**
 * @author:周庆伟
 * @date: 2020/12/3
 */
@Component
@ConfigurationProperties(prefix = "spring.datasource")
@NoArgsConstructor
@Data
public class DruidConfig {
    private String masterUrl;
    private String slaveUrl;
    private String driverClassName;
    private String username;
    private String password;
    private int initialSize;
    private int minIdle;
    private int maxActive;
    private int maxWait;
    private int timeBetweenEvictionRunsMillis;
    private int minEvictableIdleTimeMillis;
    private String validationQuery;
    private boolean testWhileIdle;
    private boolean testOnBorrow;
    private boolean testOnReturn;
    private boolean poolPreparedStatements;
    private String filters;
    private int maxPoolPreparedStatementPerConnectionSize;
    private boolean useGlobalDataSourceStat;
    private String connectionProperties;

}

```

#### MyDruidConfig.java

```java
package cn.qingweico.config;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.support.http.StatViewServlet;
import com.alibaba.druid.support.http.WebStatFilter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.Primary;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.sql.DataSource;
import java.sql.SQLException;
import java.util.Collections;
import java.util.HashMap;
import java.util.Map;

/**
 * @author 周庆伟
 * @date: 2020/10/14
 */
@Configuration
@EnableTransactionManagement
public class MyDruidConfig {

    DruidConfig druidConfig;

    @Autowired
    public void setDruidConfig(DruidConfig druidConfig) {
        this.druidConfig = druidConfig;
    }

    @Bean(name = "masterDataSource")
    @Primary
    public DataSource masterDataSource() throws SQLException {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setUrl(druidConfig.getMasterUrl());
        return getDataSource(druidDataSource);
    }

    @Bean(name = "slaveDataSource")
    public DataSource slaveDataSource() throws SQLException {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setUrl(druidConfig.getSlaveUrl());
        return getDataSource(druidDataSource);
    }


    private DataSource getDataSource(DruidDataSource druidDataSource) throws SQLException {
        druidDataSource.setDriverClassName(druidConfig.getDriverClassName());
        druidDataSource.setUsername(druidConfig.getUsername());
        druidDataSource.setPassword(druidConfig.getPassword());
        druidDataSource.setInitialSize(druidConfig.getInitialSize());
        druidDataSource.setMinIdle(druidConfig.getMinIdle());
        druidDataSource.setMaxActive(druidConfig.getMaxActive());
        druidDataSource.setMaxWait(druidConfig.getMaxWait());
        druidDataSource.setTimeBetweenEvictionRunsMillis(druidConfig.getTimeBetweenEvictionRunsMillis());
        druidDataSource.setMinEvictableIdleTimeMillis(druidConfig.getMinEvictableIdleTimeMillis());
        druidDataSource.setValidationQuery(druidConfig.getValidationQuery());
        druidDataSource.setTestWhileIdle(druidConfig.isTestWhileIdle());
        druidDataSource.setTestOnBorrow(druidConfig.isTestOnBorrow());
        druidDataSource.setTestOnReturn(druidConfig.isTestOnReturn());
        druidDataSource.setPoolPreparedStatements(druidConfig.isPoolPreparedStatements());
        druidDataSource.setFilters(druidConfig.getFilters());       druidDataSource.setMaxPoolPreparedStatementPerConnectionSize(druidConfig.getMaxPoolPreparedStatementPerConnectionSize());
        druidDataSource.setUseGlobalDataSourceStat(druidConfig.isUseGlobalDataSourceStat());
        druidDataSource.setConnectionProperties(druidConfig.getConnectionProperties());
        return druidDataSource;
    }


    @Bean
    public ServletRegistrationBean<StatViewServlet> statViewServlet() {
        ServletRegistrationBean<StatViewServlet> bean = new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");
        Map<String, String> maps = new HashMap<>(5);
        maps.put("loginUsername", "admin");
        maps.put("loginPassword", "123456");
        maps.put("allow", "");
        maps.put("deny", "");
        bean.setInitParameters(maps);
        return bean;
    }

    @Bean
    public FilterRegistrationBean<WebStatFilter> webStatFilter() {
        FilterRegistrationBean<WebStatFilter> bean = new FilterRegistrationBean<>(new WebStatFilter());
        bean.setUrlPatterns(Collections.singletonList("/*"));
        Map<String, String> maps = new HashMap<>(2);
        maps.put("exclusion", "*.js/,*.css,/druid/*");
        bean.setInitParameters(maps);
        return bean;
    }
}
```

#### DynamicDataSourceConfig.java

```java
package cn.qingweico.config;

import cn.qingweico.dao.spilt.DynamicDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.util.HashMap;
import java.util.Map;

/**
 * @author:周庆伟
 * @date: 2020/12/3
 */
@Configuration
public class DynamicDataSourceConfig {

   @Bean
   public DynamicDataSource dynamicDataSource(DataSource masterDataSource, DataSource slaveDataSource){
       DynamicDataSource dynamicDataSource = new DynamicDataSource();
       Map<Object, Object> map = new HashMap<>(5);
       map.put("master",masterDataSource);
       map.put("slave",slaveDataSource);
       dynamicDataSource.setTargetDataSources(map);
       return dynamicDataSource;

   }
}

```

#### DynamicDataSource.java

```java
package cn.qingweico.dao.spilt;

import org.springframework.jdbc.datasource.lookup.AbstractRoutingDataSource;

/**
 * mysql主从复制
 *
 * @author 周庆伟
 * @date: 2020/09/24
 */
public class DynamicDataSource extends AbstractRoutingDataSource {
    @Override
    protected Object determineCurrentLookupKey() {
        return DynamicDataSourceHolder.getDbType();
    }
}

```

#### DynamicDataSourceHolder.java

```java
package cn.qingweico.dao.spilt;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * mysql主从复制
 *
 * @author 周庆伟
 * @date: 2020/09/24
 */
public class DynamicDataSourceHolder {
    private static final Logger logger = LoggerFactory.getLogger(DynamicDataSourceHolder.class);
    private static final ThreadLocal<String> CONTEXT_HOLDER = new ThreadLocal<>();
    public static final String DB_MASTER = "master";
    public static final String DB_SLAVE = "slave";

    public static String getDbType() {
        String db = CONTEXT_HOLDER.get();
        if (db == null) {
            db = DB_MASTER;
        }
        return db;
    }

    public static void setDbType(String dbType) {
        logger.debug("所使用的数据源是:" + dbType);
        CONTEXT_HOLDER.set(dbType);
    }

    public static void clearDbType() {
        CONTEXT_HOLDER.remove();
    }
}
```

#### DynamicDataSourceInterceptor.java

```java
package cn.qingweico.dao.spilt;

import org.apache.ibatis.executor.Executor;
import org.apache.ibatis.executor.keygen.SelectKeyGenerator;
import org.apache.ibatis.mapping.BoundSql;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.mapping.SqlCommandType;
import org.apache.ibatis.plugin.*;
import org.apache.ibatis.session.ResultHandler;
import org.apache.ibatis.session.RowBounds;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.transaction.support.TransactionSynchronizationManager;

import java.util.Locale;
import java.util.Properties;

/**
 * mysql主从复制
 *
 * @author 周庆伟
 * @date: 2020/09/25
 */
@Intercepts({@Signature(type = Executor.class, method = "update", args = {MappedStatement.class, Object.class}),
        @Signature(type = Executor.class, method = "query", args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class})})
public class DynamicDataSourceInterceptor implements Interceptor {
    private static final Logger logger = LoggerFactory.getLogger(DynamicDataSourceInterceptor.class);

    private static final String REGEX = ".*insert\\u0020.*|.*update\\0020.*|.*delete\\0020.*";

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        boolean synchronizationActive = TransactionSynchronizationManager.isSynchronizationActive();
        Object[] objects = invocation.getArgs();
        MappedStatement mappedStatement = (MappedStatement) invocation.getArgs()[0];
        String lookupKey = DynamicDataSourceHolder.DB_MASTER;
        if (!synchronizationActive) {
            if (mappedStatement.getSqlCommandType().equals(SqlCommandType.SELECT)) {
                if (mappedStatement.getId().contains(SelectKeyGenerator.SELECT_KEY_SUFFIX)) {
                    lookupKey = DynamicDataSourceHolder.DB_MASTER;
                } else {
                    BoundSql boundSql = mappedStatement.getSqlSource().getBoundSql(objects[1]);
                    String sql = boundSql.getSql().toLowerCase(Locale.CHINA).replaceAll("\\t\\n\\r", " ");
                    if (sql.matches(REGEX)) {
                        lookupKey = DynamicDataSourceHolder.DB_MASTER;
                    } else {
                        lookupKey = DynamicDataSourceHolder.DB_SLAVE;
                    }
                }
            }
        } else {
            lookupKey = DynamicDataSourceHolder.DB_MASTER;
        }
        logger.debug("设置[{}] use[{}] strategy,SqlCommandType[{}]......", mappedStatement.getId(), lookupKey, mappedStatement.getSqlCommandType().name());
        DynamicDataSourceHolder.setDbType(lookupKey);
        return invocation.proceed();
    }
    @Override
    public Object plugin(Object target) {
        if (target instanceof Executor) {
            return Plugin.wrap(target, this);
        } else {
            return target;
        }
    }
    @Override
    public void setProperties(Properties properties) {}
}
```

## SpringBoot启动配置原理

### SpringBoot启动类

```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

/**
 * @author 周庆伟
 */

@SpringBootApplication
public class SpringBootDataApplication {

    public static void main(String[] args) {
        //返回ioc容器
        ConfigurableApplicationContext run =  SpringApplication.run(SpringBootDataApplication.class, args);

       //查看ioc容器中的组件
        String[] beans = run.getBeanDefinitionNames();
        for(String bean : beans){
            System.out.println(bean);
        }
    }

}
```

```java
public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
    return run(new Class[]{primarySource}, args);
}
```

```java
public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
    return (new SpringApplication(primarySources)).run(args);
}
```

先创建SpringApplication对象再运行run方法

- 创建SpringApplication对象

### SpringApplication构造函数

```java
public SpringApplication(Class<?>... primarySources) {
   this(null, primarySources);
}
```

```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
   this.resourceLoader = resourceLoader;
   Assert.notNull(primarySources, "PrimarySources must not be null");
   this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
    /*判断是否是web环境*/
   this.webApplicationType = WebApplicationType.deduceFromClasspath();
    /*从类路径下加载META-INF/spring.factories中配置的所有的ApplicationContextInitializer类*/
   setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
    /*从类路径下加载META-INF/spring.factories中配置的所有的ApplicationListener类*/
   setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
    /*从多个配置类中找到含有main方法的类为主配置类*/
   this.mainApplicationClass = deduceMainApplicationClass();
}
```

### WebApplicationType.deduceFromClasspath()

```java
static WebApplicationType deduceFromClasspath() {
   if (ClassUtils.isPresent(WEBFLUX_INDICATOR_CLASS, null) && !ClassUtils.isPresent(WEBMVC_INDICATOR_CLASS, null)
         && !ClassUtils.isPresent(JERSEY_INDICATOR_CLASS, null)) {
      return WebApplicationType.REACTIVE;
   }
   for (String className : SERVLET_INDICATOR_CLASSES) {
      if (!ClassUtils.isPresent(className, null)) {
         return WebApplicationType.NONE;
      }
   }
   return WebApplicationType.SERVLET;
}
```

```java
private static final String[] SERVLET_INDICATOR_CLASSES = { "javax.servlet.Servlet",
      "org.springframework.web.context.ConfigurableWebApplicationContext" };
private static final String WEBMVC_INDICATOR_CLASS = "org.springframework.web.servlet.DispatcherServlet";
private static final String WEBFLUX_INDICATOR_CLASS = "org.springframework.web.reactive.DispatcherHandler";
private static final String JERSEY_INDICATOR_CLASS = "org.glassfish.jersey.servlet.ServletContainer";
```

### getSpringFactoriesInstances()

```java
private <T> Collection<T> getSpringFactoriesInstances(Class<T> type) {
    return getSpringFactoriesInstances(type, new Class<?>[] {});
}
```

```java
private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
   ClassLoader classLoader = getClassLoader();
   // Use names and ensure unique to protect against duplicates
   Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
   List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
   AnnotationAwareOrderComparator.sort(instances);
   return instances;
}
```

###  loadFactoryNames()

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
   String factoryTypeName = factoryType.getName();
   return loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
}
```

### loadSpringFactories()

```java
private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
   MultiValueMap<String, String> result = cache.get(classLoader);
   if (result != null) {
      return result;
   }

   try {
      Enumeration<URL> urls = (classLoader != null ?
            classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
            ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
      result = new LinkedMultiValueMap<>();
      while (urls.hasMoreElements()) {
         URL url = urls.nextElement();
         UrlResource resource = new UrlResource(url);
         Properties properties = PropertiesLoaderUtils.loadProperties(resource);
         for (Map.Entry<?, ?> entry : properties.entrySet()) {
            String factoryTypeName = ((String) entry.getKey()).trim();
            for (String factoryImplementationName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
               result.add(factoryTypeName, factoryImplementationName.trim());
            }
         }
      }
      cache.put(classLoader, result);
      return result;
   }
   catch (IOException ex) {
      throw new IllegalArgumentException("Unable to load factories from location [" +
            FACTORIES_RESOURCE_LOCATION + "]", ex);
   }
}
```

```java
public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
```

### ApplicationContextInitializer类

```properties
org\springframework\boot\spring-boot\2.3.2.RELEASE\
    spring-boot-2.3.2.RELEASE.jar!\
        META-INF\
            spring.factories
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.context.ConfigurationWarningsApplicationContextInitializer,\
org.springframework.boot.context.ContextIdApplicationContextInitializer,\
org.springframework.boot.context.config.DelegatingApplicationContextInitializer,\
org.springframework.boot.rsocket.context.RSocketPortInfoApplicationContextInitializer,\
org.springframework.boot.web.context.ServerPortInfoApplicationContextInitializer
```

```properties
org\springframework\boot\spring-boot-autoconfigure\2.3.2.RELEASE\
    spring-boot-autoconfigure-2.3.2.RELEASE.jar!\
        META-INF\
            spring.factories
# Initializers
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener
```

### ApplicationListener类

```properties
org\springframework\boot\spring-boot\2.3.2.RELEASE\
    spring-boot-2.3.2.RELEASE.jar!\
        META-INF\
            spring.factories
# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.ClearCachesApplicationListener,\
org.springframework.boot.builder.ParentContextCloserApplicationListener,\
org.springframework.boot.cloud.CloudFoundryVcapEnvironmentPostProcessor,\
org.springframework.boot.context.FileEncodingApplicationListener,\
org.springframework.boot.context.config.AnsiOutputApplicationListener,\
org.springframework.boot.context.config.ConfigFileApplicationListener,\
org.springframework.boot.context.config.DelegatingApplicationListener,\
org.springframework.boot.context.logging.ClasspathLoggingApplicationListener,\
org.springframework.boot.context.logging.LoggingApplicationListener,\
org.springframework.boot.liquibase.LiquibaseServiceLocatorApplicationListener
```

```properties
org\springframework\boot\spring-boot-autoconfigure\2.3.2.RELEASE\
    spring-boot-autoconfigure-2.3.2.RELEASE.jar!\
        META-INF\
            spring.factories
# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.autoconfigure.BackgroundPreinitializer
```

### deduceMainApplicationClass()

```java
private Class<?> deduceMainApplicationClass() {
   try {
      StackTraceElement[] stackTrace = new RuntimeException().getStackTrace();
      for (StackTraceElement stackTraceElement : stackTrace) {
         if ("main".equals(stackTraceElement.getMethodName())) {
            return Class.forName(stackTraceElement.getClassName());
         }
      }
   }
   catch (ClassNotFoundException ex) {
      // Swallow and continue
   }
   return null;
}
```

- 运行run方法

```java
public ConfigurableApplicationContext run(String... args) {
   StopWatch stopWatch = new StopWatch();
   stopWatch.start();
   ConfigurableApplicationContext context = null;
   Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
   configureHeadlessProperty();
   /*从类路径下加载META-INF/spring.factories中配置的所有的 SpringApplicationRunListener类*/
   SpringApplicationRunListeners listeners = getRunListeners(args);
    /*运行所有的 SpringApplicationRunListener.strating()方法*/
   listeners.starting();
   try {
      ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
       /*创建和配置环境*/
      ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
      configureIgnoreBeanInfo(environment);
      Banner printedBanner = printBanner(environment);
      /*创建IOC容器*/
      context = createApplicationContext();
      exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
            new Class[] { ConfigurableApplicationContext.class }, context);
       /*准备上下文环境*/
      prepareContext(context, environment, listeners, applicationArguments, printedBanner);
       /*扫描并加载ioc容器中所有的组件*/
      refreshContext(context);
       /*完成对ioc容器中所有组件的加载*/
      afterRefresh(context, applicationArguments);
      stopWatch.stop();
      if (this.logStartupInfo) {
         new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
      }
      listeners.started(context);
       /*从ioc容器中获取所有的ApplicationRunner和CommandLineRunner*/
      callRunners(context, applicationArguments);
   }
   catch (Throwable ex) {
      handleRunFailure(context, ex, exceptionReporters, listeners);
      throw new IllegalStateException(ex);
   }

   try {
      listeners.running(context);
   }
   catch (Throwable ex) {
      handleRunFailure(context, ex, exceptionReporters, null);
      throw new IllegalStateException(ex);
   }
    /*返回ioc容器*/
   return context;
}
```

### SpringApplicationRunListener类

```properties
org\springframework\boot\spring-boot\2.3.2.RELEASE\
    spring-boot-2.3.2.RELEASE.jar!\
        META-INF\
            spring.factories
# Run Listeners
org.springframework.boot.SpringApplicationRunListener=\
org.springframework.boot.context.event.EventPublishingRunListener
```

### listeners.starting()

```java
void starting() {
   for (SpringApplicationRunListener listener : this.listeners) {
      listener.starting();
   }
}
```

### ConfigurableEnvironment prepareEnvironment()

```java
private ConfigurableEnvironment prepareEnvironment(SpringApplicationRunListeners listeners,
                                                   ApplicationArguments applicationArguments) {
    // Create and configure the environment
    ConfigurableEnvironment environment = getOrCreateEnvironment();
    configureEnvironment(environment, applicationArguments.getSourceArgs());
    ConfigurationPropertySources.attach(environment);
    listeners.environmentPrepared(environment);
    bindToSpringApplication(environment);
    if (!this.isCustomEnvironment) {
        environment = new EnvironmentConverter(getClassLoader()).convertEnvironmentIfNecessary(environment,
                                                                                               deduceEnvironmentClass());
    }
    ConfigurationPropertySources.attach(environment);
    return environment;
}
```

#### listeners.environmentPrepared()

```java
void environmentPrepared(ConfigurableEnvironment environment) {
   for (SpringApplicationRunListener listener : this.listeners) {
      listener.environmentPrepared(environment);
   }
}
```

###  printBanner()

```properties
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.2.RELEASE)
```

### createApplicationContext()

```java
protected ConfigurableApplicationContext createApplicationContext() {
   Class<?> contextClass = this.applicationContextClass;
   if (contextClass == null) {
      try {
         switch (this.webApplicationType) {
         case SERVLET:
            contextClass = Class.forName(DEFAULT_SERVLET_WEB_CONTEXT_CLASS);
            break;
         case REACTIVE:
            contextClass = Class.forName(DEFAULT_REACTIVE_WEB_CONTEXT_CLASS);
            break;
         default:
            contextClass = Class.forName(DEFAULT_CONTEXT_CLASS);
         }
      }
      catch (ClassNotFoundException ex) {
         throw new IllegalStateException(
               "Unable create a default ApplicationContext, please specify an ApplicationContextClass", ex);
      }
   }
   return (ConfigurableApplicationContext) BeanUtils.instantiateClass(contextClass);
}
```

```java
public static final String DEFAULT_SERVLET_WEB_CONTEXT_CLASS = "org.springframework.boot."
      + "web.servlet.context.AnnotationConfigServletWebServerApplicationContext";
```

```java
public static final String DEFAULT_REACTIVE_WEB_CONTEXT_CLASS = "org.springframework."
      + "boot.web.reactive.context.AnnotationConfigReactiveWebServerApplicationContext";
```

```java
public static final String DEFAULT_CONTEXT_CLASS = "org.springframework.context."
			+ "annotation.AnnotationConfigApplicationContext";
```

###  prepareContext()

```java
private void prepareContext(ConfigurableApplicationContext context, ConfigurableEnvironment environment,
      SpringApplicationRunListeners listeners, ApplicationArguments applicationArguments, Banner printedBanner) {
   context.setEnvironment(environment);
   postProcessApplicationContext(context);
   applyInitializers(context);
   listeners.contextPrepared(context);
   if (this.logStartupInfo) {
      logStartupInfo(context.getParent() == null);
      logStartupProfileInfo(context);
   }
   // Add boot specific singleton beans
   ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
   beanFactory.registerSingleton("springApplicationArguments", applicationArguments);
   if (printedBanner != null) {
      beanFactory.registerSingleton("springBootBanner", printedBanner);
   }
   if (beanFactory instanceof DefaultListableBeanFactory) {
      ((DefaultListableBeanFactory) beanFactory)
            .setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
   }
   if (this.lazyInitialization) {
      context.addBeanFactoryPostProcessor(new LazyInitializationBeanFactoryPostProcessor());
   }
   // Load the sources
   Set<Object> sources = getAllSources();
   Assert.notEmpty(sources, "Sources must not be empty");
   load(context, sources.toArray(new Object[0]));
   listeners.contextLoaded(context);
}
```

#### listeners.contextPrepared()

```java
void contextPrepared(ConfigurableApplicationContext context) {
   for (SpringApplicationRunListener listener : this.listeners) {
      listener.contextPrepared(context);
   }
}
```

#### listeners.contextLoaded()

```java
void contextLoaded(ConfigurableApplicationContext context) {
    for (SpringApplicationRunListener listener : this.listeners) {
        listener.contextLoaded(context);
    }
}
```

```java
2020-08-07 09:26:00.064  INFO 19028 --- [           main] com.example.SpringBootDataApplication    : Starting SpringBootDataApplication on LAPTOP-LIEB0H0I with PID 19028 (C:\Users\周庆伟\java\springbootdata\target\classes started by 周庆伟 in C:\Users\周庆伟\java\springbootdata)
2020-08-07 09:26:00.563  INFO 19028 --- [           main] com.example.SpringBootDataApplication    : No active profile set, falling back to default profiles: default

```

### listeners.started()

```java
void started(ConfigurableApplicationContext context) {
   for (SpringApplicationRunListener listener : this.listeners) {
      listener.started(context);
   }
}
```

### callRunners

```java
private void callRunners(ApplicationContext context, ApplicationArguments args) {
   List<Object> runners = new ArrayList<>();
   runners.addAll(context.getBeansOfType(ApplicationRunner.class).values());
   runners.addAll(context.getBeansOfType(CommandLineRunner.class).values());
   AnnotationAwareOrderComparator.sort(runners);
   for (Object runner : new LinkedHashSet<>(runners)) {
      if (runner instanceof ApplicationRunner) {
         callRunner((ApplicationRunner) runner, args);
      }
      if (runner instanceof CommandLineRunner) {
         callRunner((CommandLineRunner) runner, args);
      }
   }
}
```

### listeners.running()

```java
void running(ConfigurableApplicationContext context) {
   for (SpringApplicationRunListener listener : this.listeners) {
      listener.running(context);
   }
}
```

## SpringBoot缓存

![image-20200807145952386](https://cdn.qingweico.cn/blog/6043572d360785be54a8d7ed.png)

CachingManager管理多个Cache组件,而每个组件都有自己的唯一一个名称

```java
/*StudentService*/
@Cacheable(cacheNames = {"studentInfo"} ,key ="#id")
public Student findById(int id){
    System.out.println("连接数据库啦！！");
    return studentDao.findById(id);
}
```

```java
/*标注在启动类上表示开启缓存*/
@EnableCaching
```

### @Cacheable注解

```java
public @interface Cacheable {
    @AliasFor("cacheNames")
    String[] value() default {};

    @AliasFor("value")
    String[] cacheNames() default {};

    String key() default "";          //指定缓存数据的key。默认为方法参数的值
                                      
    String keyGenerator() default "";

    String cacheManager() default "";

    String cacheResolver() default "";

    String condition() default "";

    String unless() default "";

    boolean sync() default false;
}

```

```java
String[] cacheNames() default {}; //指定缓存组件的名称 等价于value
```

```java
String key() default "";           //指定缓存数据的key。默认为方法参数的值
```

```properties
#指定key时可以使用spEL表达式
    key ="#root.methodName"     //当前被调用的方法名
    key ="#root.method.name"    //当前被调用的方法
    key ="#root.target"         //当前被调用的目标对象
    key ="#root.targetClass"    //当前被调用的目标对象类
    key ="#root.args[0]"        //当前被调用方法的参数列表
    key ="#root.caches[0].name" //当前方法调用的缓存列表
    key ="#result"              //当前方法执行返回的值
    key ="#参数" key ="#a0"      //当前参数的值 0代表参数的索引·
```

```java
/*key和keyGenerator只可以选择一个使用*/
String keyGenerator() default ""; //key的生成器
```

```java
String cacheManager() default ""; //指定缓存管理器
```

```java
/*cacheManager和cacheResolver只可以使用一个*/
String cacheResolver() default "";//指定缓存解析器
```

```java
String condition() default "";   //指定的条件满足时才会缓存方法的返回值
```

```java
String unless() default "";      //指定的条件不满足时才会缓存方法的返回值
```

### 自动配置类CacheAutoConfiguration

```java
static class CacheConfigurationImportSelector implements ImportSelector {
   @Override
   public String[] selectImports(AnnotationMetadata importingClassMetadata) {
      CacheType[] types = CacheType.values();
      String[] imports = new String[types.length];
      for (int i = 0; i < types.length; i++) {
         imports[i] = CacheConfigurations.getConfigurationClass(types[i]);
      }
      return imports;
   }
}
```

```properties
/*imports*/
org.springframework.boot.autoconfigure.cache.GenericCacheConfiguration
org.springframework.boot.autoconfigure.cache.JCacheCacheConfiguration
org.springframework.boot.autoconfigure.cache.EhCacheCacheConfiguration
org.springframework.boot.autoconfigure.cache.HazelcastCacheConfiguration
org.springframework.boot.autoconfigure.cache.InfinispanCacheConfiguration
org.springframework.boot.autoconfigure.cache.CouchbaseCacheConfiguration
org.springframework.boot.autoconfigure.cache.RedisCacheConfiguration
org.springframework.boot.autoconfigure.cache.CaffeineCacheConfiguration
org.springframework.boot.autoconfigure.cache.SimpleCacheConfiguration
org.springframework.boot.autoconfigure.cache.NoOpCacheConfiguration
```

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnMissingBean(CacheManager.class)
@Conditional(CacheCondition.class)
class SimpleCacheConfiguration {
   @Bean
    /*给容器中注册了ConcurrentMapCacheManager,可以用来获取和创建ConcurrentMapCache类型的缓存组件*/
   ConcurrentMapCacheManager cacheManager(CacheProperties cacheProperties,
         CacheManagerCustomizers cacheManagerCustomizers) {
      ConcurrentMapCacheManager cacheManager = new ConcurrentMapCacheManager();
      List<String> cacheNames = cacheProperties.getCacheNames();
      if (!cacheNames.isEmpty()) {
         cacheManager.setCacheNames(cacheNames);
      }
      return cacheManagerCustomizers.customize(cacheManager);
   }
}
```

```java
public class ConcurrentMapCache extends AbstractValueAdaptingCache {
	private final String name;
     /*数据保存在ConcurrentMap中*/
	private final ConcurrentMap<Object, Object> store;
}
```

### 运行流程

```java
/*CacheAutoConfiguration*/
@Override
@Nullable
public Cache getCache(String name) {
   Cache cache = this.cacheMap.get(name);
   if (cache == null && this.dynamic) {
      synchronized (this.cacheMap) {
         cache = this.cacheMap.get(name);
         if (cache == null) {
            cache = createConcurrentMapCache(name);
            this.cacheMap.put(name, cache);
         }
      }
   }
   return cache;
}
```

目标方法运行之前会先去查询缓存组件,根据cacheNames查询,第一次查询cache为空则创建cache并放在ConcurrentMap中

```java
/*ConcurrentMapCache*/
protected Cache createConcurrentMapCache(String name) {
   SerializationDelegate actualSerialization = (isStoreByValue() ? this.serialization : null);
   return new ConcurrentMapCache(name, new ConcurrentHashMap<>(256), isAllowNullValues(), actualSerialization);
}
```

根据key（默认方法参数的值 ,若参数有多个则传入多个参数包装的对象）来查询value,若value为空则调用目标方法

```java
/*ConcurrentMapCache*/
@Override
@Nullable
protected Object lookup(Object key) {
   return this.store.get(key);
}
```

将目标的返回值放入缓存中

```java
/*ConcurrentMapCache*/
@Override
public void put(Object key, @Nullable Object value) {
   this.store.put(key, toStoreValue(value));
}
```

### @CachePut注解

用于更新数据   先调用目标方法,再将方法的返回值更新到缓存中（不仅可以更新数据库中的数据还可以更新缓存中的数据）

```java
/*注意:key的值要和查询方法key的值保持一致(保持数据一致性)*/
@CachePut(cacheNames = {"studentInfo"}, key = "#student.id")
public Student update(Student student){
    System.out.println("更新数据啦！");
    studentDao.update(student);
    /*一定要返回值 用来更新缓存*/
    return student;
}
```

@Cacheable是先根据key查询缓存,若该key对应的值为null再调用目标方法,最后才将数据put进缓存中

@CachePut则是直接调用目标方法,再将方法的值put进缓存中,会覆盖key对应的值

### @CacheEvict注解

用于清除缓存

```java
@CacheEvict(cacheNames = {"studentInfo"} ,key = "#id" ,allEntries = true ,beforeInvocation = true)
public void deleteCacheById(Integer id){
}
```

`allEntries属性默认为false即根据只key来删除相对应的缓存,而true即代表删除该缓存组件中所有的缓存`

`beforeInvocation属性默认为false,代表方法执行完后清除缓存 true代表方法执行前删除缓存,无论方法非正常执行完都要清除缓存`

### Caching注解

可以指定多个属性

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Caching {

   Cacheable[] cacheable() default {};

   CachePut[] put() default {};

   CacheEvict[] evict() default {};

}
```

```java
@Caching(
        cacheable = {@Cacheable(value = "studentInfo" ,key = "#student.id")},
        put = {@CachePut(value = "studentInfo" ,key = "#result.name")}
)
public Student findNameById(Student student){ 
    studentDao.findNameById(student);
    return student;
}
```

### @CacheConfig注解

标注在类上,指定该类中方法共有的缓存属性

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface CacheConfig {
   String[] cacheNames() default {};
   String keyGenerator() default "";
   String cacheManager() default "";
   String cacheResolver() default "";
}
```

### Redis

#### pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

#### 配置redis主机

```yaml
spring:
  redis:
    host: 192.168.0.107
```

```java
@Autowired
StringRedisTemplate stringRedisTemplate;/*以k-v字符串的形式保存*/
@Autowired
RedisTemplate redisTemplate;            /*以k-v对象的形式保存*/
```

#### 自定义redis序列化对象的规则

保存对象默认使用jdk序列化机制

```java
/*将对象转化为json数据保存在redis中*/
package com.example.config;
import com.example.entity.Student;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
@Configuration
public class MyRedisConfig {
    @Bean
    public RedisTemplate<Object ,Student> myRedisTemplate(RedisConnectionFactory redisConnectionFactory){
        RedisTemplate<Object ,Student> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        Jackson2JsonRedisSerializer<Student> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Student.class);
        redisTemplate.setDefaultSerializer(jackson2JsonRedisSerializer);
        return redisTemplate;
    }
    @Bean
    public RedisCacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        RedisCacheConfiguration cacheConfiguration = RedisCacheConfiguration.defaultCacheConfig()
            .entryTtl(Duration.ofDays(1))
            .disableCachingNullValues()
            .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer()));
        return RedisCacheManager.builder(connectionFactory).cacheDefaults(cacheConfiguration).build();
    }
}
```

```java
/*测试*/
@Autowired
StudentDao studentDao;
@Autowired
RedisTemplate<Object , Student> myRedisTemplate;
@Test
void contextLoads() {
    Student student = studentDao.findById(2);
    myRedisTemplate.opsForValue().set("student",student);
}
```

#### 实体类实现Serializable接口

#### RedisCacheConfiguration

默认会将CacheName作为key的前缀

```java
@Deprecated
public RedisCacheConfiguration prefixKeysWith(String prefix) {
   Assert.notNull(prefix, "Prefix must not be null!");
   return computePrefixWith((cacheName) -> prefix);
}
```

- 当容器中存在多个cacheManager时,需要在目标方法的类上使用`@CacheConfig`注解指明cacheManager

- 直接在组件方法上使用`@Primary`注解表明这是默认的cacheManager

```java
/*不使用@cacheabe注解*/
@Autowired
RedisCacheManager cacheManager;
public Student findById(int id){
    System.out.println("连接数据库啦！！");
    Student student = studentDao.findById(id);
    /*设置缓存组件的名称*/
    Cache cache = cacheManager.getCache("studentInfo");
    /*设置缓存的key*/
    cache.put("key",student);
    return student;
}
```

