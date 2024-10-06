# 目录

[TOC]

## SpringMVC

### SpringMVC环境搭建

#### pom.xml

```xml
<!--导入Spring MVC开发环境所要的依赖-->
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>5.2.2.RELEASE</version>
</dependency>
<dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-webmvc</artifactId>
       <version>5.2.2.RELEASE</version>
</dependency>
```

#### web.xml

```xml
<!--配置前端控制器-->
<servlet>
    <servlet-name>dispatcherServlet</servlet-name>
     <!--SpringMVC提供的类-->
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class> 
   <!--提供全局的初始化参数-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <!--服务器初始化时加载类路径下springmvc.xml的配置文件-->
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <!--一般情况下当有请求时服务器才	会创建Servlet对象-->
    <!--此时应该当服务器完成初始化时就要创建Servlet对象,完成对springmvc配置文件的加载,完成注解扫描-->
    <load-on-startup>1</load-on-startup>
</servlet>
<!--配置解决中文乱码的过滤器-->
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
<servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

#### resource目录下配置springmvc.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           https://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/mvc
                           http://www.springframework.org/schema/mvc/spring-mvc.xsd">
</beans>
```

```xml
<!--开启注解扫描-->
<context:component-scan base-package="cn.qingweico"/>
```

```java
@Controller  //表明当前类是一个控制类
```

```java
@RequestMapping(path = "/hello")
public String hello() {
    System.out.println("请求成功!");
    /*mvc下成功跳转页面 success.jsp文件*/
    return "success";  
}
```

#### 配置视图解析器

```xml
 <!--视图解析器对象-->
<bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver"> 
     <!--prefix:前缀 寻找类路径下/WEB-INF/pages/下的视图-->
    <property name="prefix" value="/WEB-INF/pages/"/>
    <!--suffix:后缀 匹配以.jsp后缀结束的视图-->
    <property name="suffix" value = ".jsp"/>
</bean>
```

```xml
<!--开启SpringMvc注解的支持-->
<mvc:annotation-driven/>
```

### ServletInvocableHandlerMethod

专门用于处理 Servlet 请求, 负责调用控制器的方法来处理 HTTP 请求, 并处理方法的参数和返回值

### DispatcherServlet 前端控制器

DispatcherServlet是整个流程控制的核心,当用户请求到达前端控制器时,由它调用其他组件来处理用户的请求,正是DispatcherServlet的存在降低了组件之间的耦合性

### HandlerMapping 处理器映射器

HandlerMapping根据用户的请求找到JHandler 即处理器

### Handler 处理器

它是我们开发中要编写的具体业务控制器,由DispatcherServlet把用户请求转发到Handler,由Handler对具体的用户请求进行处理

### HandlerAdapter 处理器适配器

通过HandlAdapter对处理器进行执行,这是适配器模式的应用,通过扩展适配器可以对更多类型的处理器进行执行

### ViewResolver 视图解析器

View Resolver负责将处理的结果生成视图。View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址,再生成具体的视图对象,最后对视图进行渲染将处理结果通过页面展示给用户

### RequestMapping的属性

```properties
@RequestMapping(path = '')   //用于建立请求URL和请求方法之间的对应关系
#放在类上表示请求的一级目录,放在方法上表示请求的二级目录
@RequestMapping(value = '')  //path和value表示相同的作用,即请求的URL地址,当只有URL时可以省略
@RequestMapping(value = '', method = RequestMethod.GET) 
method:表示请求的方式 (GET,HEAD,POST,PUT,PATCH,DELETE,OPTIONS,TRACE)
params:表示指定限制请求参数的条件
headers：用于指定限制请求消息头的条件
#上面四个属性出现二个以及以上时属性之间是与的关系
```

### 请求参数绑定

- 基本数据类型和String类型

  ```java
  @RequestMapping(path = "/hello", method = RequestMethod.GET)
  public String hello(String username, int password) {
      System.out.println("你好" + username + "!" + " 你的密码是" + password);
      return "success";
  }
  ```

  ```jsp
  <!--提交数据的键要和参数的名称要相同-->
  <a href="hello?username=jack&password=21">登陆</a>
  ```

- JavaBean

  ```java
  @RequestMapping(value = "/test", method = {RequestMethod.POST})
  public String test(Account account) {
      System.out.println(account);
      return "success";
  }
  ```

  ```jsp
  <form action="test" method="post">
      <!--name的值与实体类中的属性名保持一致-->
      <!--而且实体类中一定要有属性的set方法,这样才能把数据封装到实体类中-->
      姓名:<input type="text" name="username"><br>  
      密码:<input type="text" name="password">
      <input type="submit" value="提交">
  </form>
  ```
  
- 实体类中含有引用类型

  ```jsp
  <form action="test" method="post">
      姓名:<input type="text" name="username"><br>
      密码:<input type="text" name="password"><br>
      <!--使用实体类中引用属性名称.引用实体类属性-->
      学号:<input type="text" name="user.number"><br>
      年龄:<input type="text" name="user.age"><br>
      <input type="submit" value="提交">
  </form>
  ```
  
- 集合类型

  ```java
  private  List<User> list;
  private Map<Integer,User> map;
  ```

  ```jsp
  <form action="test" method="post">
      姓名:<input type="text" name="username"><br>
      密码:<input type="text" name="password"><br>
      <!--将数据封装到User对象存入List集合的第一个位置-->
      学号:<input type="text" name="list[0].number"><br>
      年龄:<input type="text" name="list[0].age"><br>
      <!--将数据封装到User对象存入键为0的位置-->
      学号:<input type="text" name="map[0].number"><br>
      年龄:<input type="text" name="map[0].age"><br>
      <input type="submit" value="提交">
  </form>
  ```


### 获取原生API的方式

```java
@RequestMapping(value = "/servlet")
public String servlet(HttpServletRequest request) {
    System.out.println(request);
    HttpSession session = request.getSession();
    System.out.println(session);
    ServletContext servletContext = session.getServletContext();
    System.out.println(servletContext);
    return "success";
}
```

### 注解RequestParam

```java
@RequestMapping(path = "/hello",method = RequestMethod.GET)
    //@RequestMapping参数中name和value属性的作用是一样的,且当只有一个参数时可以省略
    //参数required类型为boolean默认是true,当为false时,请求中可以没有name属性
    public String hello(@RequestParam(name = "name")String username) {
    System.out.println("你好" + username + "!");
    return "success";
}
```

```jsp
<a href="hello?name=helloWorld">Hello</a> <!--URL之间不要留空格-->
```

### RequestBody注解

主要用来接收前端传递给后端的json字符串中的数据的,且@RequestBody最多只能有一个

```JAVA
@RequestMapping(value = "/body")
    public String body(@RequestBody String body) {
    System.out.println(body);
    return "success";
}
```

### RESTFUL风格（PathVariable）

```java
@RequestMapping(value = "/rest/{id}")
    public String rest(@PathVariable(name = "id") String id) {
    System.out.println(id);
    return "success";
}
```

```jsp
<a href="rest/我是id">Restful</a>
```

### RequestHeader注解

```JAVA
/*获取请求头的值*/
@RequestMapping(value = "/header")
    public String head(@RequestHeader(value = "Cookie") String header) {
    System.out.println(header);
    return "success";
}
```

### CookieValue注解

```java
/*获取指定cookie的值*/
@RequestMapping(value = "/Cookie")
    public String Cookie(@CookieValue(value = "JSESSIONID") String cookie) {
    System.out.println(cookie);
    return "success";
}
```

### ModelAttribute注解

该注解既可以修饰方法也可以修饰参数

修饰方法时代表该方法会在控制器方法执行之前执行

修饰参数时可以获取指定的数据给参数赋值

```jsp
<!--当用户输入的数据不完整时,即没有输入学号和年龄时,可以通过先执行带有ModelAttribute注解的方法来获取学号和年龄,从而保证数据的完整性-->
<form action="model" method="post">
    用户名：<label><input type="text" name="username"></label><br>
    密码:<label><input type="text" name="password"></label><br>
    学号:<input type="text" name="user.number"><br>
    年龄:<input type="text" name="user.age"><br>
    <input type="submit" value="提交">
</form>
```

- 有返回值

```java
@ModelAttribute 
    public Account modelAttribute(String username){
    Account account = new Account();
    //模拟从数据库中查询
    User user = findByName(username);
    user.setNumber("007");
    user.setAge("21");
    account.setUser(user);
    return account;
}
```

```java
@RequestMapping("/model")
    public String model(Account account){
    System.out.println(account);
    return "success";
}
```

- 无返回值

```java
@ModelAttribute
public void modelAttribute(Map<String, Account> map,String username) {
    Account account = new Account();
    User user = findByName(username);
    user.setAge("21");
    user.setNumber("007");
    account.setUser(user);
    map.put("user", account); //使用map集合把account对象存入map集合中
}
```

```java
@RequestMapping("/model")
    public String model(@ModelAttribute("user") Account account){
    System.out.println(account);
    return "success";
}
```

### SessionAttributes注解

```java
@RequestMapping("/sessionAttribute")
    public String session(ModelMap model){
    /*以键值对的形式将message和SessionAttributes存入到request域对象中*/
    model.addAttribute("message","SessionAttribute");
    /*从session域中取得message的值*/
    model.get("message");
    return "success";
}
```

```java
//将message在存入到session域中
@SessionAttributes(value = "message")// 标注在类上 value可以换成names names和value可以省略
```

```java
//删除session域对象
@RequestMapping("/deleteSessionAttribute")
    public String deleteSession(SessionStatus sessionStatus){
    sessionStatus.setComplete();
    return "success";
}
```

### ModelAndView

```java
@RequestMapping("/ModelAndView")
    public ModelAndView modelAndView(){
    ModelAndView modelAndView = new ModelAndView();
    User user = new User();
    user.setNumber("007");
    user.setAge("21");
    /*将user存入request域对象中*/
    modelAndView.addObject("user",user);
    /*设置成功要跳转的页面*/
    modelAndView.setViewName("success");
    return modelAndView;
}
```

### 使用关键字进行重定向和请求转发

```java
@RequestMapping("/forwardAndRedirect")
    public String forwardAndDirect(){
    return  "forward:WEB-INF/pages/success.jsp"; //请求转发
  //return "redirect:test.jsp";                  重定向  重定向无法访问WEB-INF下的文件
}
```

### 响应JSON数据

```xml
<!--导入java解析json数据格式的依赖-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.8</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.8</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.9.8</version>
</dependency>
```

在webapp下新建js文件夹导入jquery文件

```xml
<!--在springmvc.xml配置文件中过滤js文件夹下所有的静态资源 默认会被前端控制器拦截-->
<mvc:resources mapping="/js/**" location="/js/"/>
<!--二者作用相等-->
<mvc:default-servlet-handler />
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<script src="js/JQuery.3.4.1.js"></script>
<script>
    $(function () {
        $("#btn").on("click",function () {
                    $.ajax({
                         type: "post",
                         dataType: "json",
                         url: "/testAjax",
                         data: '{"name":"22","number":122}',
                         contentType: "application/json;charset=utf-8",
                         success:function (data) {
                         console.log(data);   //name:"张三",number:7
                         }
                    });
            }
        )
    });
</script>
<body>
<button id="btn">发送ajax请求</button>
</body>
</html>
```

```java
@RequestMapping("/testAjax")
/*@RequestBody 会将请求的json数据（String类型）格式转换为java对象封装到Student对象中*/
/*ResponseeBody  会将javaBean转换为json响应到前端*/
public @ResponseBody Student ajax(@RequestBody Student student) {
    System.out.println(student);
    student.setName("张三");
    student.setNumber(7);
    return student;
}
```

***注意:实体类中不能有有参构造方法***

### 文件上传

```xml
<!--导入文件上传的依赖-->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.3</version>
</dependency>
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.4</version>
</dependency>
```

form表单的要求 enctype属性必须是"multipart/form-data" method属性必须是post 且提供一个文件选择域`<input type = "file">`

#### 传统web文件上传

```java
@RequestMapping("/upload")
public String upload(HttpServletRequest request) throws Exception{
    System.out.println("文件上传中...");
    String path = request.getSession().getServletContext().getRealPath("/upload/");
    DiskFileItemFactory diskFileItemFactory = new DiskFileItemFactory();
    ServletFileUpload servletFileUpload = new ServletFileUpload(diskFileItemFactory);
    List<FileItem> items = servletFileUpload.parseRequest(request);
    for(FileItem item : items){
        String fileName = item.getName();
        item.write(new File(path,fileName));
    }
    System.out.println("文件上传成功...");
    return "success";
}
```

#### mvc文件上传

```xml
<!--配置文件解析器对象-->
<bean id="multipartResolver" 
       class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
       <property name="maxUploadSize" value="10000000"/><!--配置上传文件的最大值 单位字节-->
</bean>
```

```java
@RequestMapping("/mvcUpload")
public String MvcUpload(HttpServletRequest request, MultipartFile upload) throws IOException {
    System.out.println("文件上传中...");
    String path = request.getSession().getServletContext().getRealPath("/mvcUpload/");
    String fileName = upload.getOriginalFilename();
    Objects.requireNonNull(fileName);
    String newFileName = UUID.randomUUID() + fileName.substring(fileName.lastIndexOf("."));
    upload.transferTo(new File(path, newFileName));
    System.out.println("文件上传成功");
    return "success";
}
```

```jsp
<form enctype="multipart/form-data" action="mvcUpload" method="post">
    <label>选择文件：</label>
    <!--name的属性值必须和参数MultipartFile的值一样-->
    <input type="file" name="upload"><br>
    <input type="submit" value="提交">
</form>
```

### spring异常处理

```java
// 自定义异常类
public class SysException extends Exception {
    private String errorMessage;
    public SysException(String errorMessage) {
    	this.errorMessage = errorMessage;
    }
    public String getErrorMessage() {
    	return errorMessage;
    }
    public void setErrorMessage(String errorMessage) {
   		this.errorMessage = errorMessage;
    }
}
```

```java
// 配置异常处理类
public class SysExceptionResolver implements HandlerExceptionResolver {
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
    SysException sysException = null;
    if(e instanceof  SysException){
    	sysException = (SysException) e;
    }else {
    	sysException = new SysException("系统正在维护。。。");
    }
    ModelAndView mv = new ModelAndView();
    mv.addObject("errorMessage",sysException.getErrorMessage());
    /*遇到异常跳到错误页面,并显示错误信息*/
    mv.setViewName("error");
    return mv;
    }
}
```

```xml
<!--配置异常解析器-->
<!--将异常处理类加入到ioc容器中-->
<bean id="exceptionResolver" class="cn.qingweico.exception.SysExceptionResolver"/>
```

### spring拦截器

spirng拦截器和过滤器的区别：

- 过滤器可以在任何的java web项目下使用,属于servlet技术,可以过滤任何请求
- 拦截器只能在springmvc环境下使用,属于springmvc框架自带的技术,只能对Controller层请求有效

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <!--配置要拦截的方法-->
        <!--/**表示拦截任何的请求-->
        <mvc:mapping path="/**"/>
        <!--配置不要拦截的方法-->
        <!--<mvc:exclude-mapping path=""/>-->
        <bean class="cn.qingweico.interceptor.MyInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

#### 自定义实现HandlerInterceptor 接口的类

```java
public class MyInterceptor implements HandlerInterceptor {
    // 拦截请求,访问controller之前
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("我是拦截器的预处理方法...");
        request.getRequestDispatcher("upload.jsp").forward(request,response);
        return true;  //true表示放行 false表示不放行
    }
	// 请求访问到controller之后, 渲染视图之前
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
       System.out.println("我是拦截器的后处理方法...");
       request.getRequestDispatcher("upload.jsp").forward(request,response);
    }
	// 请求访问到controller之后, 渲染视图之后
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("我是拦截器的最后处理方法...");
    }
}
```

![springmvcInterceptor](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ea52f3ec2a9a83be5c042d0.png)