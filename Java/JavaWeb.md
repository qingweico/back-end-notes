# 目录

[TOC]

### Servlet

```java
public interface Servlet {
    void init(ServletConfig servletConfig) throws ServletException;
    ServletConfig getServletConfig();
    void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;
    String getServletInfo();
    void destroy();
}
```

```xml
<!--web.xml配置WebServlet-->
<servlet>
    <servlet-name>servlet</servlet-name>
    <servlet-class>com.java.servlet.MyServlet</servlet-class>
    <init-param>
        <param-name>username</param-name>
        <param-value>admin</param-value>
    </init-param>
    <init-param>
        <param-name>password</param-name>
        <param-value>123456</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>servlet</servlet-name>
    <url-pattern>/servlet</url-pattern>
</servlet-mapping>
```

```java
/*基于注解的WebServlet*/
@WebServlet("/servlet")
public class MyServlet implements Servlet {
    ServletConfig servletConfig
}
```

```java
/*基于注解的WebServlet会返回Servlet的名称: 带的包的类名*/
servletConfig.getServletName() 
```

```xml
<!--使用web.xml配置WebServlet时会返回标签servlet-name中的值-->
<servlet-name>servlet</servlet-name>
```

```java
 /*获取带参数的值(web.xml)*/
servletConfig.getInitParameter("username")
```

```java
/*获取所有initParameter的值  一般用作遍历初始化参数*/
Enumeration<String> enumeration = servletConfig.getInitParameterNames();
while(enumeration.hasMoreElements()){
    /*根据键来遍历值*/
    String result = enumeration.nextElement();
    System.out.println(result + ": " + servletConfig.getInitParameter(result));
}
```

```java
/* 返回ServletContext对象 是Servlet的上下文 */
servletConfig.getServletContext();
/* 返回参数所在的项目打包后(~out\artifacts\Web_war_exploded\file\)的绝对路径 */
servletConfig.getServletContext().getRealPath("/file/");
// 二者应该等价
request.getServletContext().getRealPath("/file/");
```

### ServletContext与ServletConfig的区别

```properties
前者相当于全局变量 而后者相当于局部变量且两者都是接口
每个Servlet都有相对应的ServletConfig
每个ServletContext则对应整个Web应用 多个Servlet实例对应一个ServletContext
```

### HttpServletRequest和ServletRequest的区别

前者继承于后者

```java
ServletRequest servletRequest;
HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
HttpSession session = httpServletRequest.getSession();
session.setAttribute("message","This is a message!");
session.getAttribute("message");
```

### ServletRequest和ServletConfig

都是接口,且都含有ServletContext

### servlet的生命周期

```java
package com.java.servlet;
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import java.io.IOException;
@WebServlet("/hello")
public class HelloServlet implements Servlet {
    /*Tomcat通过反射机制创建了Servlet对象*/
    public HelloServlet(){
        System.out.println("创建了Servlet对象........");
    }
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("已经完成对Servlet容器的初始化....");
    }
    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("执行Servlet有关的方法......");

    }
    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("释放了Servlet对象.........");

    }
}
```

Tomcat服务器启动时会创建servlet对象(通过反射创建),且只会创建一次,该对象是单例的,当服务器关闭时,destroy方法会执行

```java
package com.java.servlet;
import java.lang.reflect.Constructor;
public class ReflectCreateObject {
    public static void main(String[] args) {
        try {
            Class <?>servlet = Class.forName("com.java.servlet.HelloServlet");
            Constructor<?> constructor = servlet.getConstructor();
            Object obj = constructor.newInstance();
            System.out.println(obj);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

### Jsp九大内置对象

1. request
2. response 
3. pageContext
4. session 
5. application 
6. config 
7. out 
8. page   
9. exception

### request

- String getParameter(String key) 获取客户端传来的数据 
- void setAttribute(String key,Object value) 通过键值对来保存数据 
- Object getAttribute(String key) 通过键来取值 
- ***RequestDispatcher  getRequestDispatcher(String path) 返回一个RequestDispatcher对象(请求转发)***  
- String[] getParameterValues() 获取客户端传来的多个参数 
- void setCharacterEncoding(String charset) 指定每次请求的编码

### response

#### 重定向和请求转发之间的区别(sendRedirect和RequestDispatcher)

首先请求转发是将同一个请求传给下一个页面 而重定向将一个新的请求传给页面,之前的请求生命周期已结束

请求转发是在同一个请求在服务器之间传递,即浏览器的地址栏不变,也叫服务器跳转 

重定向则是浏览器重新发送一个请求来访问跳转之后的资源,浏览器地址栏发生变化,也叫客户端跳转   

#### session

seeion常用的方法: 

- String getId() 获取session的ID 
- void setMaxInactiveInterval(int interval) 设置session失效的时间 单位为秒 
- int getMaxInactiveInterval() 获取当前session失效时间  
- void invalidate() 设置当前session立即失效 
- void setAttribute(String key,Object value) 通过键值对的形式来存储数据 
- Object getAttribute(String key) 通过键来获取相应的数据 
- void removeAttribute(String key) 通过键来删除对应的数据  

#### cookie  

- 创建Cookie

```java
Cookie cookie = new Cookie("name","jack");
response.addCookie(cookie);
```

- 读取Cookie 

  ```java
  Cookie[] cookies = coorequest.getCookies();
  for(Cookie cookie:cookies){
      out.write(cookie.getName() + "---" + cookie.getValue() + "<br/>"); 
  }
  ```

- void  setMaxAge(int age) 设置cookie的有效时间 单位为秒 

- int getMaxAge() 设置cookie的有效时间    

#### sesson和cookie的区别  

- session是保存在服务端而cookie是保存在浏览器端 
- session会随着会话的结束而销毁cookie却可以长期保存在浏览器中 
- seeion保存的是Object对象而cookie则保存的是文本文件即String类型 
- session保存的是重要的信息而cookie保存的是不重要的信息   

#### session和cookie的周期

session会随着服务器的重启而销毁,也就是一次会话结束。客户端的关闭也会立即销毁session对象 

cookie不会随着服务器的重启而销毁,而默认是关闭客户端则会立即销毁,可以通过设置setMaxAge()来设置销毁时间,即使关闭客户端cookie也不会销毁 

### Jsp内置对象作用域  

- page: 对应的内置对象是pageContext 只在当前页面内有效 
- request: 对应的内置对象是request 在一次请求内有效 
- session: 对应的内置的对象是session 在一次会话内有效 
- application: 对应的内置对象是application 在整个WEB应用中都有效 

#### 访问量统计 

```jsp
<%
    Integer count = (Integer)application.getAttribute("count");
    if(count == null){
        count = 1;
    } else {
        count++;
    }
    application.setAttribute("count",count);
%>
当前网页的访问量是: <%= count%>
```

### El表达式

***Expression Language 表达式语言***

```jsp
<%
request.setAttribute("name","jack");
%>
${"name"} //相当于request.getAttribute("name")
${pageScope.name}
${requestScope.name}
${sessionScope.name}
${applicationScope.name}
//通过设置相应的域对象的范围来取得相应的值
```

如果EL表达式要取得对象的内容应该使用.或者是[],这里和js里对象的属性取得是一样的,如果属性是变量或者属性中含有空格或特殊字符,此时应该使用[]而不是.

**EL表达式只能在jsp页面中使用,无法在servlet中使用,也无法在HTML中单独使用**

### JSTL表达式

Jsp Standard Tag Library   ***jsp 标准标签库*** 

使用时应该先导入相应的jar。 在WEB-INF目录下创建lib文件夹 再将相应的jar包导入到IDEA环境中

然后在jsp页面导入相应的包 

```jsp
<%@ taglib prefix="c" tagdir="jar包地址"%>
```

***prefix表示前缀,即匹配以c开头的语法***

#### JSTL的优点

1. 提供了统一的标准库 
2. 可以用于编写各种动态功能  

jstl常用的标签有set out remove catch 

#### set 向域对象中添加数据

```jsp
<%
request.setAttribute(key,value)
%>
<c:set var="name",value="jack" scope="request"></c:set>
$(request.name) 
```

#### out 输出域对象中的数据 

```html
<c:set var="name" value="jack"></c:set>
<c:out value="{name}" default="未定义"  ></c:out>
```

#### remove 删除域对象中的数据

```html
<c:set var="name" value="jack"></c:set>
<c:remove var="name" value="jack"></c:remove>
<c:out var="{name}" default=="未定义"></c:out>
```

#### catch 可以捕获jsp中的异常

```jsp
<c:catch var="error">
    <%
     int a=1/0;
    %> 
</c:catch>
${error}
```

### Filter生命周期 

- Filter接口包括三个方法  default void init()  ,void foFilter() , default void destroy()  
- 主要的方法就是doFilter() 且在实现该接口的类中必须重写该方法 而其他的两种方法可以不用重写(被default修饰) 
- 和Servlet一样,每次服务器启动时都会通过反射机制且通过无参构造函数创建一个实例对象  
- init方法和destroy方法只会执行一次,而doFilter则会执行多次 

#### Filter的主要功能 

1. 解决中文乱码问题 

2. 屏蔽敏感词汇

   ```java
   @WebFilter("/test")
   public class WordFilter implements Filter {
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
          servletRequest.setCharacterEncoding("UTF-8");
          String name = servletRequest.getParameter("name");
          name = name.replaceAll("敏感词","***");
          servletRequest.setAttribute("name",name);
          filterChain.doFilter(servletRequest,servletResponse);
       }
   }
   // Servlet
   @WebServlet("/test")
   public class Test extends HttpServlet {
       @Override
       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            String name = (String) req.getAttribute("name");
            System.out.println(name);  
       }
   }
   ```
   
3. 控制用户的访问权限  

   description: 用户请求下载资源时会检查用户是否登陆,若以登录则允许下载否则跳转到登陆页面

   **Filter**

   ```java
   package com.java.servlet;
   import javax.servlet.*;
   import javax.servlet.annotation.WebFilter;
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   import javax.servlet.http.HttpSession;
   import java.io.IOException;
   @WebFilter("/Download.jsp") 
   public class Filter implements javax.servlet.Filter {
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           HttpServletRequest request  = (HttpServletRequest) servletRequest;
           String username =  request.getParameter("username");
           String password = request.getParameter("password");
           HttpSession session = request.getSession();
           String name = (String)session.getAttribute("name");
           // 用户信息不存在则立即返回登陆界面
           if(name == null){    
               HttpServletResponse response = (HttpServletResponse) servletResponse;
               response.sendRedirect("login.jsp");
           }
           else{
               // 取消拦截 用户可以访问下载页面
               filterChain.doFilter(request,servletResponse);
           }
   
       }
   }
   ```
   

**Servlet**

```java
package com.java.servlet;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;
@WebServlet("/login")
public class Test extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        if(username.equals("admin") && password.equals("123456")){
            HttpSession session = req.getSession();
            session.setAttribute("name",username);
            // 登陆成功则进入下载界面
            resp.sendRedirect("Download.jsp");
        }
        else{
            resp.sendRedirect("login.jsp");
        }
    }
}
```

   **登陆**

   ```html
<%--
     Created by IntelliJ IDEA.
     User: 周庆伟
     Date: 2020/3/4
     Time: 20:34
     To change this template use File | Settings | File Templates.
   --%>
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   <form action="/login" method="post">
       <input type="text" name="username"><br>
       <input type="password" name="password">
       <input type="submit" value="登陆">
   </form>
   </body>
   </html>
   ```

   **下载的资源**

   ```html
<%--
     Created by IntelliJ IDEA.
     User: 周庆伟
     Date: 2020/3/4
     Time: 22:46
     To change this template use File | Settings | File Templates.
   --%>
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   <a href="#">资源一</a>
   <a href="#">资源二</a>
   <a href="#">资源三</a>
   </body>
   </html>
   ```

   ### 文件上传

   先从外部导入commons-io 和commons-fileupload 两个jar包  

   **Servlet**

   ```java
package com.java.servlet;
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.*;
import java.util.List;
@WebServlet("/upload")
public class Upload extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        try {
            DiskFileItemFactory factory = new DiskFileItemFactory();
            ServletFileUpload fileUpload = new ServletFileUpload(factory);
            fileUpload.setHeaderEncoding("UTF-8");
            List<FileItem> items = fileUpload.parseRequest(req);
            for (FileItem item : items) {
                InputStream inputStream = item.getInputStream();
                /* 获取上传的文件名 */
                String fileName  = item.getName();
                /* 获取file文件夹的绝对路径 */
                String path = req.getServletContext().getRealPath("/file/" + fileName);
                OutputStream outputStream = new FileOutputStream(path);
                int len;
                while((len = inputStream.read()) != -1){
                    outputStream.write(len);
                }
                outputStream.close();
                inputStream.close();
                outputStream.close();
                System.out.println("上传成功");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
   ```

```java
/* 简单一点的方法 */
package com.java.servlet;
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.*;
import java.util.List;
@WebServlet("/upload")
public class Upload extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        try {
            DiskFileItemFactory factory = new DiskFileItemFactory();
            ServletFileUpload fileUpload = new ServletFileUpload(factory);
            fileUpload.setHeaderEncoding("UTF-8");
            List<FileItem> items = fileUpload.parseRequest(req);
            String path = req.getServletContext().getRealPath("/file/");
            for (FileItem item : items) {
                String fileName = item.getName();
                item.write(new File(path,fileName));
                System.out.println("上传成功");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}               
```

**HTML** 

   ```html
<%--
     Created by IntelliJ IDEA.
     User: 周庆伟
     Date: 2020/3/5
     Time: 11:21
     To change this template use File | Settings | File Templates.
   --%>
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   <form action="/upload" method="post" enctype="multipart/form-data">
       <input type="file" name="file">
       <input type="submit"  value="提交">
   </form>
   </body>
   </html>
   ```

### 下载文件

**Servlet**

```java
package com.java.servlet;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import java.net.URLEncoder;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
@WebServlet("/download")
public class Download extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String method = req.getParameter("method");
        String fileName = "";
        switch (method){
            case "img":
                fileName = "1.png";
                break;
            case "text":
                fileName = "1.txt";
                break;
        }
        resp.setHeader("Content-Disposition" ,"attachment;filename;filename" + URLEncoder.encode(fileName));
        OutputStream outputStream = resp.getOutputStream();
        /* 获取目标资源在服务器的绝对路径 */
        String path = req.getServletContext().getRealPath("file/" + fileName);
        InputStream inputStream = new FileInputStream(path);
        int len;
        while((len=inputStream.read()) != -1){
            outputStream.write(len);
        }
        inputStream.close();
        outputStream.close();
    }
}
```

**HTML**

```html
<%--
  Created by IntelliJ IDEA.
  User: 周庆伟
  Date: 2020/3/5
  Time: 13:35
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<a href="/download?method=img">1.png</a>
<a href="/download?method=text">1.txt</a>
</body>
</html>
```

### Ajax

Ajax : Asynchronous Javascript and XML

```java
package com.java.servlet;
import net.sf.json.JSONObject;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
@WebServlet("/ajax")
public class Ajax extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        UserInfo userInfo = new UserInfo(001,"小黑",21);
        resp.setCharacterEncoding("UTF-8");
        /*通过JSONObject可以将java对象转化为JSON格式*/
        JSONObject jsonObject =JSONObject.fromObject(userInfo);
        resp.getWriter().write(jsonObject.toString());
    }
}
```

```html
<%--
  Created by IntelliJ IDEA.
  User: 周庆伟
  Date: 2020/3/5
  Time: 16:18
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <script src="js/JQuery.3.4.1.js"></script>
    <script>
        $(function () {
            let btn = $("#btn");
            btn.click(function () {
                $.ajax({
                    url: '/ajax',
                    dataType: 'json',
                    success: function (data) {
                        $("#id").val(data.id);
                        $("#username").val(data.username);
                        $("#password").val(data.password);
                    }
                });
            });
        });
    </script>
</head>
<body>
id :<input  id="id" type="text" name="id" value=""><br>
username :<input  id="username" type="text" value=""><br>
password: <input  id="password" type="text" value=""><br>
<input id="btn" type="submit" value="提交">
</body>
</html>
```

### 学生信息管理系统

#### 使用Student类来封装学生信息

```java
package com.java.servlet;
public class Student {
    private int id;
    private String name;
    private int number;
    public Student(int id,String name,int number){
        this.id = id;
        this.name = name;
        this.number = number;
    }
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public void setNumber(int number) {
        this.number = number;
    }
    public int getNumber() {
        return number;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

#### 对JDBC代码块进行封装复用

```java
package com.java.servlet;
import java.sql.*;
public class JDBCTools {
    static  final  String JDBC_DRIVE="com.mysql.cj.jdbc.Driver";
    static final String DB_URL= "jdbc:mysql://localhost/test?useSSL=false&serverTimezone=UTC";
    static final String USER = "root";
    static final String PASS = "123456";
    private static Connection conn;
    static {
      try {
          Class.forName(JDBC_DRIVE);
      }catch (ClassNotFoundException e){
          e.printStackTrace();
      }
    }
    public static Connection connectSQL(){
       try {
           conn = DriverManager.getConnection(DB_URL,USER,PASS);
       }catch(SQLException e){
           e.printStackTrace();
       }
       return conn;
    }
    public static void release(Connection conn,Statement state){
       try{
           if(conn != null){
               conn.close();
           }
           if(state != null){
               state.close();
           }
       }catch(SQLException e){
           e.printStackTrace();
       }
    }
}
```

#### 使用java来连接数据库进行crud操作

```java
package com.java.servlet;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
public class MySQL  {
    public static List<Student> readStudent(){
        List<Student>list = new ArrayList<>();
        Connection conn = null;
        Statement state = null;
        try {
            conn = JDBCTools.connectSQL();
            state = conn.createStatement();
            String sql = "Select * from student";
            ResultSet resultSet = state.executeQuery(sql);
            Student student;
            while(resultSet.next()){
                int id = resultSet.getInt(1);
                String name = resultSet.getString(2);
                int number = resultSet.getInt(3);
                student = new Student(id,name,number);
                list.add(student);
            }
        }catch (SQLException | NullPointerException e){
            e.printStackTrace();
        } finally{
           JDBCTools.release(conn,state);
        }
        return list;
    }
    public static void addStudent(int id,String name,int number) {
        Connection conn;
        Statement state;
        try {
           conn =JDBCTools.connectSQL();
            state = conn.createStatement();
            String sql = "insert  into student values ('"+id+"','"+name+"','"+number+"')";
            state.executeUpdate(sql);
        }catch (SQLException|NullPointerException e ){
            e.printStackTrace();
        }
    }
    public static void removeStudent(int id){
        Connection conn = null;
        Statement state = null;
        try {
           conn = JDBCTools.connectSQL();
            state = conn.createStatement();
            String sql = "delete  from student where 编号= '"+ id +"' ";
            state.executeUpdate(sql);
        }catch (SQLException e ){
            e.printStackTrace();
        }finally {
           JDBCTools.release(conn, state);
        }
    }
    public static void updateStudent(int id, String name, int number){
        Connection conn = null;
        Statement state = null;
        try {
            conn = JDBCTools.connectSQL();
            state = conn.createStatement();
            String sql = "update student set 姓名 = '" + name + "' , 学号=   '" + number + "'  where 编号 = '" + id + "' ";
            state.executeUpdate(sql);
        }catch (SQLException e ){
            e.printStackTrace();
        }finally {
          JDBCTools.release(conn,state);
        }
    }
    public static Student findById(int id){
        Student student = null;
        Connection conn = null;
        Statement state = null;
        try {
            conn = JDBCTools.connectSQL();
            state = conn.createStatement();
            String sql = "select * from student where 编号 =  '"+id+"'  ";
            ResultSet resultSet= state.executeQuery(sql);
            while(resultSet.next()) {
                int ID = resultSet.getInt(1);
                String name = resultSet.getString(2);
                int number = resultSet.getInt(3);
                student = new Student(ID, name, number);
            }
        }catch (SQLException e ){
            e.printStackTrace();
        }finally {
           JDBCTools.release(conn,state);
        }
        return  student;
    }
}
```

#### 使用Servlet来完成与前端数据进行交接

```java
package com.java.servlet;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;
@WebServlet("/mysql")
public class StudentServlet extends HttpServlet {
    Student student;
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          String method = req.getParameter("method");
          if(method == null){
              method = "read";
          }
          switch (method){
              case "read":
                  List<Student> list = MySQL.readStudent();
                  req.setAttribute("list",list);
                  req.getRequestDispatcher("index.jsp").forward(req,resp);
                  break;
              case "remove":
                  String idStr = req.getParameter("id");
                  int id = Integer.parseInt(idStr);
                  MySQL.removeStudent(id);
                  student = MySQL.findById(id);
                  resp.sendRedirect("/mysql");
                  break;
              case "findById":
                   idStr = req.getParameter("id");
                   id = Integer.parseInt(idStr);
                   Student student = MySQL.findById(id);
                   req.setAttribute("student",student);
                   req.getRequestDispatcher("update.jsp").forward(req,resp);
                  break;
          }
    }
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String method = req.getParameter("method");
        if(method == null){
            method = "add";
        }
        switch (method){
            case "add":
                String idStr = req.getParameter("id");
                String name = req.getParameter("name");
                String numberStr = req.getParameter("number");
                int id = Integer.parseInt(idStr);
                int number = Integer.parseInt(numberStr);
                MySQL.addStudent(id,name,number);
                student = new Student(id,name,number);
                break;
            case "update" :
                idStr = req.getParameter("id");
                name = req.getParameter("name");
                numberStr = req.getParameter("number");
                id = Integer.parseInt(idStr);
                number = Integer.parseInt(numberStr);
                MySQL.updateStudent(id,name,number);
               break;
        }
        resp.sendRedirect("/mysql");
    }
}
```

#### 信息展示页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" pageEncoding="UTF-8" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
  <head>
    <title>Title</title>
  </head>
  <body>
<h1>学生信息管理系统</h1>
<hr>
<table>
  <tr>
    <th>编号</th>
    <th>姓名</th>
    <th>学号</th>
    <th>操作</th>
  </tr>
  <tr>
    <jsp:useBean id="list" scope="request" type="java.util.List"/>
    <c:forEach items="${list}" var="list">
    <td>${list.id}</td>
    <td>${list.name}</td>
    <td>${list.number}</td>
      <td>
        <a href="create.jsp">添加</a>
        <a href="/mysql?method=remove&id=${list.id}">删除</a>
        <a href="/mysql?method=findById&id=${list.id}">修改</a>
      </td>
  </tr>
  </c:forEach>
</table>
  </body>
</html>
```

#### 信息修改页面(update)

```jsp
<%--
  Created by IntelliJ IDEA.
  User: 周庆伟
  Date: 2020/3/4
  Time: 17:03
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="/mysql?method=update" method="post">
    编号:<input type="text" name="id" value="${student.id}" readonly><br>
    姓名:<input type="text" name="name" value="${student.name}"><br>
    学号:<input type="text" name="number" value="${student.number}"><br>
    <input type="submit" value="修改">
</form>
</body>
</html>
```

#### 信息增加页面(create)

```jsp
<%--
  Created by IntelliJ IDEA.
  User: 周庆伟
  Date: 2020/3/4
  Time: 15:48
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="/mysql" method="post">
    编号:<input type="text" name="id"><br>
    姓名:<input type="text" name="name"><br>
   学号:<input type="text" name="number"><br>
    <input  type="submit" value="提交">
</form>
</body>
</html>
```

#### 过滤器(避免中文乱码)

```java
package com.java.servlet;
import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;
@WebFilter("/mysql")
public class CharacterFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletRequest.setCharacterEncoding("UTF-8");
        filterChain.doFilter(servletRequest,servletResponse);
    }
}
```

### 数据库连接池(C3p0)

首先导入第三方jar包 

- c3p0-0.9.5.5.jar 
- c3p0-oracle-thin-extras-0.9.5.5.jar 
- mchange-commons-java-0.2.19.jar 

在java工程src下新建c3p0-config.xml XML文件  注意文件名不能改 只能为c3p0-config.xml

```xml
<?xml version='1.0' encoding='UTF-8'?>
<c3p0-config>
    <named-config name="test">
        <!--指定连接源的基本属性-->
        <property name="user">user</property>
        <property name="password">passwordty>
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost/test?useSSL=false&amp;serverTimezone=UTC</property>

        <!--若数据库中连接数不足时 一次向数据库服务器申请请求多个连接-->
        <property name="acquireIncrement">5</property>
        <!--初始化数据库连接池的连接数量-->
        <property name="initialPoolSize">5</property>
        <!--数据库连接池中的最小的数据库连接数-->
        <property name="minPoolSize">2</property>
        <!--数据库连接池中的最大的数据库连接数-->
        <property name="maxPoolSize">20</property>
    </named-config>
</c3p0-config>
```

```java
package com.java.servlet;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import java.sql.Connection;
import java.sql.SQLException;
public class DataSourceTest {
    public static void main(String[] args) {
        try {
            ComboPooledDataSource dataSource = new ComboPooledDataSource("test");
            Connection connection = dataSource.getConnection();
            connection.close();
        }catch (SQLException e){
            e.printStackTrace();
        }
    }
}
```

ComboPooledDataSource有参构造函数传的参数应该XML文件中named-config name属性 而不是XML文件名

### DBUtils

首先导入jar包

ResultSetHandler接口是用来处理结果集,可以将查询到的结果转换为java对象

- BeanHandler          将结果映射成java对象 
- BeanListHandler    将结果映射成List集合 
- MapHandler           将结果映射成Map对象 
- MapListHandler     将结果映射成MapList集合   **List <Map<String,Object>> list**

```java
package com.java.servlet;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.MapListHandler;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.List;
import java.util.Map;
public class DataSourceTest {
    public static void main(String[] args) {
        try {
            ComboPooledDataSource dataSource = new ComboPooledDataSource("test");
            Connection connection = dataSource.getConnection();
            String sql = "Select * from student";
            QueryRunner queryRunner = new QueryRunner();
            List<Map<String,Object>> list = queryRunner.query(connection,sql,new MapListHandler());
            for(Map<String,Object> map :list){
                System.out.println(map);
            }
            connection.close();
        }catch (SQLException e){
            e.printStackTrace();
        }
    }
}
```

------



