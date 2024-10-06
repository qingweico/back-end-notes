# 目录

[TOC]

## MyBatis

![mybatis](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/5ea6ab97c2a9a83be5655477.png)

Mybatis是一个持久层框架,使用java编写,它封装了JDBC的很多的细节,使开发者只关注sql语句本身,而不用关注使用原生jdbc时应该具有的注册驱动,创建连接,关闭连接等繁杂的过程,它使用了ORM的思想实现了结果集的封装

ORM: Object Relational Mapping 对象关系映射

就是把数据库表的字段和实体类属性对应起来,从而对实体类进行操作就可以完成对数据库表的操作

### MyBatis环境搭建

```xml
<!--导入MyBatis运行环境依赖和数据库驱动依赖-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.1</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.19</version>
</dependency>
<!--加载src路径下的xml文件-->
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```

#### 在resources下创建MyBatis全局配置文件 mybatis-config.xml

```xml
<!--导入MyBatis主配置文件约束-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
 "http://mybatis.org/dtd/mybatis-3-config.dtd">
```

```xml
<configuration>
  <settings>
      <!--打印SQL语句-->
      <setting name="logImpl" value="STDOUT_LOGGING"/>
      <!--开启延迟加载-->
      <setting name="lazyLoadingEnabled" value="true"/>
      <!-- MyBatis自带的二级缓存  开启二级缓存-->
      <setting name="cacheEnabled" value="true"/>
  </settings>
    <!--加载类路径下jdbc连接信息的配置文件-->
    <properties resource="JDBCConfiguration.properties"/>
    <environments default="development">
        <environment id="development">
            <!--配置JDBC事物管理-->
            <transactionManager type="JDBC"/>
            <!--POLLED配置数据库连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--注册AccountRepository.xml 加载类路径下mapper映射文件-->
        <mapper resource="mappers/AccountRepository.xml"/>
    </mappers>
</configuration>
```

#### mapper文件约束

```properties
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```

### MyBatis基于实现类的方式实现查询（不常用）

```java
public interface AccountRepository {
    List<Account> findAll();
 }
```

```java
public class AccountRepositoryImpl implements AccountRepository {
    private SqlSessionFactory factory = null;
    public AccountRepositoryImpl(SqlSessionFactory factory){
        this.factory = factory;
    }
    public List<Account> read() {
        SqlSession sqlSession = factory.openSession();
        //加载src目录下mapper映射文件
        String statement = "cn.qingweico.mappers.AccountMapper.readAll""
        List<Account> account = sqlSession.selectList();
        sqlSession.close();
        return account;
    }
}
```

***使用实现类的方式时映射配置文件中的id可以不用和方法名一样***

```xml
<!--AccountMapper.xml配置文件-->
<!--namespace表示该配置文件所在的类路径,不是接口所在的路径-->
<!--基于接口的namespace则表示对应的接口所在的类路径-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.qingweico.mappers.AccountMapper">
    <cache/>
    <select id ="readAll" resultType="cn.qingweico.entity.Account">
        select * from account;
    </select>
</mapper>
```

```java
public class BasedOnTheClass {
    public static void main(String[] args) {
        /*获取配置文件输入流的两种方式*/
InputStream inputStream =  BasedOnTheClass.class.getClassLoader().getResourceAsStream("mybatis-config.xml");
        InputStream inputStream =  Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        AccountRepository accountRepository =  new AccountRepositoryImpl(sqlSessionFactory);
        //查询操作
        List<Account> list = accountRepository.findAll();
        for(Account account : list){
            System.out.println(account);
        }
    }
}
```

```xml
<!--mybatis-config.xml-->
<mappers>
        <mapper resource="cn/qingweico/mappers/AccountMapper.xml"/>
</mappers>
```

### MyBatis基于接口（Mapper代理）的方式实现查询

```xml
<!--namespace表示接口类路径下的全限定类名-->
<mapper namespace="cn.qingwei.repository.AccountRepository">
    <!--resultType表示接口中方法返回类型 不可以省略--->
    <select id="read" resultType="cn.qingweico.entity.Account" >
        select * from account;
    </select>
</mapper>
```

```java
public class Demo {
    public static void main(String[] args) {
        //以流的方式读取配置文件
        InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
        //使用构建者模式创建工厂
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //使用工厂生产sqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //使用sqlSession创建AccountRepository的代理对象
        AccountRepository accountRepository = sqlSession.getMapper(AccountRepository.class);
        //查询操作
        List<Account> list = accountRepository.read();
        for(Account account : list){
            System.out.println(account);
        }
```

***使用接口实现查询时映射文件中id的名称必须和接口中的方法名保持一致***

### Myatis cud语句

```xml
<!--parameterType表示接口中方法参数的类型 可以省略-->
<!--使用数据库自增主键-->
<!--useGeneratedKeys="true" keyProperty="" keyColumn=""-->
<insert id="insert" parameterType="cn.qingweico.entity.Account">
    <!--显示插入数据后对应的id值-->
    <selectKey keyProperty="id" resultType="int" keyColumn="id" order="AFTER">
        select last_insert_id();
    </selectKey>
    insert into account values (#{username},#{password });
</insert> 
<delete id="delete" parameterType="int">
    delete from account where id = #{id};
</delete>
<select id="findById"  parameterType="String"                         resultType="cn.qingweico.entity.Account">                                     
    select * from account where username like '%${value}%';
</select>
```

### typeAliases标签

```xml
<!--为实体类起别名-->
<!--mybatis-config.xml-->
<typeAliases>
    <typeAlias type="cn.qingweico.entity.Student" alias="Student"/>
</typeAliases>
<!--也可以指定一个包名,MyBatis 会在包名下面搜索需要的 Java Bean  此时aliae默认是实体类首字母小写-->
<typeAliases>
    <package name="cn.qingweico.entity"/>
</typeAliases>
```

### 数据库中字段名可以和实体类中属性名称不一样

```xml
<resultMap id="studentMap" type="Student">
    <!--将数据库中的字段名和实体类中的属性名建立映射关系-->
    <!--id为数据库中主键的字段名 column为数据库中非主键字段名 property为实体类中属性名称-->
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <!--实体类中含有引用类型并和数据库中其他的表建立映射关系-->
    <association property="classes" javaType="Class">
        <result column="class_id" property="classId"/>
        <result column="class_name" property="className"/>
    </association>
</resultMap>
```

<font style="color:purple;font-size:20px"> 起别名的作用是可以简写resultType中的值(接口的返回类型) 并不是可简写namespace中的值(mapper文件映射的接口所在的位置),不要混为一谈</font> 

### 连接池

可以减少获取连接所消耗的次数

一个存放很多Connection的集合容器,且线程安全（保证两个线程不能拿到同一个连接）

实现的队列的性质（先进先出）

type类型包括POOLED UNPOOLED JNDI

POOLED ：每次使用时都会从连接池中获取连接,使用完后会放入连接池

UNPOOLED：不使用连接池,每次使用都会加载驱动,创建连接

### MyBatis中的事务

```java
SqlSession sqlSession = sqlSessionFactory.openSession(true);//开启自动提交事务（不常用）
```

### 动态SQL

```java
public class Student implements Serializable {
    /**
     * 编号
     */
    private Integer id;
    /**
     * 姓名
     */
    private String name;
    /**
     * 所属班级
     */
    private Class classes;
}
```

```java
public class Class implements Serializable {
    /**
     * 班级id
     */
    private Integer classId;
    /**
     * 班级名称
     */
    private String className;
    /**
     * 班级中的学生列表
     */
    private List<Student> students;
}
```

#### if标签

```xml
<select id="findByStudent" resultType="Student">
    select * from student where
    <!--当if标签条件满足时,标签内部的条件就会加上-->
    <if test="id != null">
        id = #{id}
    </if>
    <if test="name != null">
        and name = #{name}
    </if>
    <if test="classes != null">
        and class_id = #{classes.classId}
    </if>
</select>
```

#### where标签

当id为null时,where会直接和and连接,where标签可以解决这个问题

```xml
<select id="findByStudent" parameterType="Student" resultType="Student">
    <!--不使用where标签-->
    select * from student where 1 = 1
    <if test="id != null">
        and id = #{id}
    </if>
    <if test="name != null">
        and name = #{name}
    </if>
    <if test="classes != null">
        and class_id = #{classes.classId}
    </if>
</select>
```

```xml
<select id="findByStudent" parameterType="Student" resultType="Student">
    <!--使用where标签-->
    <where>
        <if test="id != null">
            id = #{id}
        </if>
        <if test="name != null">
            and name = #{name}
        </if>
        <if test="classes != null">
       		and class_id = #{classes.classId}
   		</if>
    </where>
</select>
```

#### choose标签

```xml
<select id="findByStudent" parameterType="Student" resultType="Student">
    <!--若第一个when成立不会执行下面的when语句-->
    select * from student
    <where>
        <choose>
            <when test="id !=null">
                id = #{id}
            </when>
            <when test="name != null">
                name = #{name}
            </when>
            <when test="classes != null">
                class_id = #{classes.classId}
            </when>
        </choose>
    </where>
</select>
```

#### trim标签

```xml
<!--和where标签作用一样-->
select * from student
<!--当where和and直接相连时会把and删掉-->
<trim prefix="where" prefixOverrides="and">
    <if test="id != null">
        id = #{id}
    </if>
    <if test="name != null">
        and name = #{name}
    </if>
    <if test="classes != null">
        and class_id = #{classes.classId}
    </if>
    
</trim>
```

#### set标签

```xml
<!--当只修改数据库中某一字段时,而不用修改数据库中的全部字段-->
<update id="update" parameterType="Student">
    update student
    <set>
        <if test="name != null">
            name = #{name},
        </if>
        <if test="classes != null">
            class_id = #{classes.classId}
        </if>
    </set>
    where id = #{id}
</update>
```

#### foreach标签

可以一次性生成很多值,主要用于sql的in语句

```java
public class Student implements Serializable {
    /**
     * 编号
     */
    private Integer id;
    /**
     * 姓名
     */
    private String name;
    /**
     * 所属班级
     */
    private Class classes;
    /**
     * 编号集合
     */
    private List<Integer> ids;
```

```xml
<select id="studentList" resultType="Student" parameterMap="Student">
    select * from student
    <where>
        <!--collection表示实体类中要遍历的属性集合    item代表从集合中取出的值 可以自定义-->
        <foreach collection="ids" open="id in ("  close=")" item="id" separator=",">
            <!--#{}中的值必须和上面的item的值保持一致 这里都为id-->
            #{id}
        </foreach>
    </where>
</select>
```

```java
public void ForEachLabel() throws IOException {
        InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        StudentRepository studentRepository = sqlSession.getMapper(StudentRepository.class);
        Student student = new Student();
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);
        student.setIds(list);
        //查询id在1,2,3,4中的学生集合
        System.out.println(studentRepository.studentList(student));
}
```

### MyBatis一对一操作查询

需求：查询学生信息时要求把所在班级的信息查询出来（级联查询）

```xml
<resultMap id="studentMap" type="Student">
    <!--column表示数据库中的字段名,property表示要映射的实体类中的属性-->
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <!--assocation配置实体类中的引用类型 propety代表实体类中的属性 javaType代表属性的全限定类名-->
 	<association property="classes" javaType="Class">
        <result column="class_id" property="classId"/>
        <result column="class_name" property="className"/>
    </association>
</resultMap>
<select id="findById" resultMap="studentMap">
        select
            id,
            `name`,
            c.class_id,
            c.class_name
        from
            student s,
            tb_class c
        where
            s.class_id = c.class_id
        and
            s.id  = #{id};
</select>
```

### MyBatis一对多查询操作

需求：查询班级信息,要求把所在该班级的所有学生查出来

```java
public class Class implements Serializable {
      /**
     * 班级id
     */
    private String classId;
    /**
     * 班级名称
     */
    private String className;
    /**
     * 班级中的学生列表
     */
    private List<Student> students;
}
```

```xml
<resultMap id="classMap" type="Class">
    <id column="class_id" property="classId"/>
    <result column="class_name" property="className"/>
    <!--collection配置实体类中集合的数据类型 ofType代表集合中的泛型-->
    <collection property="students" ofType="student">
           <result column="id" property="id"/>
           <result column="name" property="name"/>
        <!--           <association property="classes" javaType="Class">-->
        <!--               <result column="class_id" property="classId"/>-->
        <!--               <result column="class_name" property="className"/>-->
        <!--           </association>-->
     </collection>
</resultMap>
<select id="findById" resultMap="classMap">
        select
            id,
            `name`,
            c.class_id,
            c.class_name
        from
            student s,
            tb_class c
        where
            s.class_id = c.class_id
        and
            c.class_id = #{id};
</select>
```

### MyBatis多对多查询操作

需求：查询客户,要求把该客户购买的货物查询出来

需求：查询货物,要求把购买该货物的客户信息查询出来

```java
public class Customer {
    /**
     * 客户id
     */
    private Integer customerId;
    /**
     * 客户姓名
     */
    private String customerName;
    /**
     * 客户购买的货物列表
     */
    private List<Goods> goodsList;
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.qingweico.repository.CustomerRepository">
    <resultMap id="customerMap" type="Customer">
        <id column="customer_id" property="customerId"/>
        <result column="customer_name" property="customerName"/>
        <collection property="goodsList" ofType="Goods">
            <result column="goods_id" property="goodsId"/>
            <result column="goods_name" property="goodsName"/>
        </collection>
    </resultMap>
    <select id="findGoodsListByCustomerId" resultMap="customerMap">
       select
            c.*,
            g.goods_id,
            g.goods_name
       from
            customer c,
            goods g,
            customer_goods cg
       where
            cg.goods_id = g.goods_id
        and
            c.customer_id = cg.customer_id
        and
            c.customer_id = #{customer_id};
    </select>
</mapper>
```

```java
public class Goods {
    /**
     * 货物id
     */
    private Integer goodsId;
    /**
     * 货物名称
     */
    private String goodsName;
    /**
     * 购买该货物的客户列表
     */
    private List<Customer> customersList;
}

```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.qingweico.repository.GoodsRepository">
    <resultMap id="goodsMap" type="Goods">
        <id column="goods_id" property="goodsId"/>
        <result column="goods_name" property="goodsName"/>
        <collection property="customerList" ofType="Customer">
            <result column="customer_id" property="customerId"/>
            <result column="customer_name" property="customerName"/>
        </collection>
    </resultMap>
    <select id="findCustomerListByGoodsId" resultMap="goodsMap">
      select
            g.goods_id,
            g.goods_name,
            c.customer_id,
            c.customer_name
      from
            customer c,
            goods g,
            customer_goods cg
      where
            c.customer_id = cg.customer_id
      and
            cg.goods_id = g.goods_id
      and
            g.goods_id = #{goodsId};
    </select>
</mapper>
```

### MyBatis延迟加载

延迟加载可以根据特定的场景来选择需要查询的表,可以减少与数据库的交互次数,从而达到提高程序的执行效率

将多表的查询分成多个单表查询

StudentRepository.xml

```xml
<resultMap id="delayMap" type="Student">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <!--select表示接口ClassRepository中的方法 column表示另一次查询的条件 从当前查询结果集中获取-->
    <association property="classes" javaType="cn.qingweico.entity.Class" select="cn.qingweico.repository.ClassRepository.delay" column="classes"/>
</resultMap>
    <select id="delay" resultMap="delayMap">
    select * from student where id = #{id};
</select>
```

ClassRepository.xml

```xml
<select id="delay" resultMap="classMap">
      select * from tb_class where class_id = #{class_id};
</select>
```

在主配置文件中开启延迟加载

```xml
<settings>  
  <!--开启延迟加载-->
  <setting name="lazyLoadingEnabled" value="true"/> 
</settings>
```

```java
@Test
public void lazyLoading() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder  = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory  = sqlSessionFactoryBuilder.build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    StudentRepository studentRepository = sqlSession.getMapper(StudentRepository.class);
    //查询id为4的学生所有的信息
    System.out.println(studentRepository.delay(4));
}
```

```shell
==>  Preparing: select * from student where id = ?; 
==> Parameters: 4(Integer)
<==    Columns: id, name, class_id
<==        Row: 4, 小明, 2
<==      Total: 1
==>  Preparing: select * from tb_class where class_id = ?; 
==> Parameters: 2(Integer)
<==    Columns: class_id, class_name
<==        Row: 2, 2班
<==      Total: 1
Student{id=4, name='小明', classes=Class{classId='2', className='2班', students=[]}}
```

```java
@Test
public void lazyLoading() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder  = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory  = sqlSessionFactoryBuilder.build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    StudentRepository studentRepository = sqlSession.getMapper(StudentRepository.class);
     //只查询id为4的学生的姓名
    System.out.println(studentRepository.delay(4).getName());
}
```

```shell
==>  Preparing: select * from student where id = ?; 
==> Parameters: 4(Integer)
<==    Columns: id, name, class_id
<==        Row: 4, 小明, 2
<==      Total: 1
小明
```

### MyBatis缓存

- MyBatis自带一级缓存 默认开启且不能关闭, 作用范围是同一个sqlSession。当从同一个sqlSession获取相同的数据时,则会使用缓存。当有cud操作时,会自动清除二级缓存,保证数据的准确性

- MyBatis自带的二级缓存,需要手动打开 作用范围是Mapper下的同一个namespace,二级缓存比一级缓存有更大的范围

  二级缓存开启的条件

  ```xml
  <!--在主配置文件中开启二级缓存-->
  <settings>
      <!-- MyBatis自带的二级缓存  开启二级缓存-->
      <setting name="cacheEnabled" value="true"/>
  </settings>
  ```

  ```xml
  <!--在相应的mapper文件中打开cache标签-->
  <cache/>
  ```

  ```java
  //相应的实体类实现序列化接口
  public class Student implements Serializable {
       /**
       * 编号
       */
      private Integer id;
      /**
       * 姓名
       */
      private String name;
      /**
       * 所属班级
       */
      private Class classes;
  }
  ```

### MyBatis注解开发

#### 基于注解的方式（修改mybatis-config.xml配置文件中mapper注册的方式）

```xml
<!--基于注解的方式-->
<!--class表示类路径下的接口的全限定类名-->
<mapper class="cn.qingweico.repository.AccountRepository"/>
<!--指定带有注解的dao接口所在的位置 mybatis会扫描该包下所有的接口-->
<package name="cn.qingweico.repository"/>
```

```java
public interface AccountRepository {
    @Select("select * from account")
    List<Account> read();
}
```

#### Mybatis建立实体类属性和数据库字段的关系映射

当实体类中的属性名可以和数据库中的字段名不一样时：

```java
@Results(id = "studentMap",value = {
    /*id为true表示主键 property表示实体类属性的值,column表示数据库字段名*/
    @Result( id = true,property = "id",column = "id"),
    @Result(property = "name",column = "name"),
})
```

```java
/*一定要实现Serializable接口*/
public class Student implements Serializable {
    /**
     * 编号
     */
    private Integer id;
    /**
     * 姓名
     */
    private String name;
    /**
     * 所属班级
     */
    private Class classes;
}
```

```java
@Select("select * from student")
/*引用上面的studentMap 将数据库中查询的数据映射到实体类中*/
@ResultMap(value = "studentMap")	
List< Student> findAll();
```

#### MyBatis建立一对一的查询（立即加载）

```java
 @Select("select * from student where id = #{id}")
    @Results(id = "studentMap", value = {
            @Result(id = true, property = "id", column = "id"),
            @Result(property = "name", column = "name"),
            //fetchType表示加载的方式 eager表示立即加载 lazy表示延迟加载
            //在一对一中使用立即加载 在多对一中使用延迟加载
            @Result(property = "classes", column = "class_id",one = @One(select = "cn.qingweico.repository.ClassRepository.findById", fetchType = FetchType.EAGER))
    })
    Student findById(Integer id);
```

```java
/*这里的#{class_id}就是上面的class_id*/
/*方法可以不用传参 根据上面的class_id条件再进行一次单表查询*/ 
@Select("select * from tb_class where class_id = #{class_id}")
@Results(id = "classMap", value = {
    @Result(property = "classId",column = "class_id"),
    @Result(property = "className",column = "class_name")
})
Class findById();
```

```java
@Test
public void basedOne2OneAnnotations() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder  = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory  = sqlSessionFactoryBuilder.build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    StudentRepository studentRepository = sqlSession.getMapper(StudentRepository.class);
    //查询id为4的学生的所有信息
    System.out.println(studentRepository.findById(4));
}
```

```xml
==>  Preparing: select * from student where id = ? 
==> Parameters: 4(Integer)
<==    Columns: id, name, class_id
<==        Row: 4, 小明, 2
====>  Preparing: select * from tb_class where class_id = ? 
====> Parameters: 2(Integer)
<====    Columns: class_id, class_name
<====        Row: 2, 2班
<====      Total: 1
<==      Total: 1
Student{id=4, name='小明', classes=Class{classId='2', className='2班', students=null}}
```

```java
@Test
public void basedOne2OneAnnotations() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder  = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory  = sqlSessionFactoryBuilder.build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    StudentRepository studentRepository = sqlSession.getMapper(StudentRepository.class);
    //只查询id为4的学生的姓名
    System.out.println(studentRepository.findById(4).getName());
}
```

```xml
==>  Preparing: select * from student where id = ? 
==> Parameters: 4(Integer)
<==    Columns: id, name, class_id
<==        Row: 4, 小明, 2
====>  Preparing: select * from tb_class where class_id = ? 
====> Parameters: 2(Integer)
<====    Columns: class_id, class_name
<====        Row: 2, 2班
<====      Total: 1
<==      Total: 1
小明
```

将`FetchType.EAGER`换为`FetchType.LAZY`再试一次

```xml
<!--已开启延迟加载-->
==>  Preparing: select * from student where id = ? 
==> Parameters: 4(Integer)
<==    Columns: id, name, class_id
<==        Row: 4, 小明, 2
<==      Total: 1
小明
```

#### MyBatis建立一对多查询

```java
@Select("select * from tb_class where class_id = #{class_id}")
    @Results(id = "classMap", value = {
            @Result(property = "classId",column = "class_id"),
            @Result(property = "className",column = "class_name"),
            @Result(property = "students", column ="class_id" ,many = @Many(select ="cn.qingweico.repository.StudentRepository.findStudentByClassId",fetchType = FetchType.LAZY))
    })
    Class findStudentListByClassId(int class_id);
```

```java
//#{class_id}就是上面的将上面查询的结果集中class_id作为本次单表查询的条件
@Select("select * from student where class_id = #{class_id}")
List<Student> findStudentByClassId()
```

```java
 @Test
    public void basedOne2ManyAnnotations() throws IOException {
        InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder  = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory  = sqlSessionFactoryBuilder.build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        ClassRepository classRepository = sqlSession.getMapper(ClassRepository.class);
        //查询二班所有的学生列表
        System.out.println(classRepository.findStudentListByClassId(2));
    }
```

```xml
==>  Preparing: select * from tb_class where class_id = ? 
==> Parameters: 2(Integer)
<==    Columns: class_id, class_name
<==        Row: 2, 2班
<==      Total: 1
==>  Preparing: select * from student where class_id = ? 
==> Parameters: 2(Integer)
<==    Columns: id, name, class_id
<==        Row: 4, 小明, 2
<==        Row: 7, 小达, 2
<==      Total: 2
Class{classId='2', className='2班', students=[Student{id=4, name='小明', classes=null}, Student{id=7, name='小达', classes=null}]}
```

```java
@Test
public void basedOne2ManyAnnotations() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder  = new SqlSessionFactoryBuilder();
    SqlSessionFactory sqlSessionFactory  = sqlSessionFactoryBuilder.build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();
    ClassRepository classRepository = sqlSession.getMapper(ClassRepository.class);
    // 只查询二班的名称
    System.out.println(classRepository.findStudentListByClassId(2).getClassName());
}
```

```xml
<!-- 开启延迟加载 -->
==>  Preparing: select * from tb_class where class_id = ? 
==> Parameters: 2(Integer)
<==    Columns: class_id, class_name
<==        Row: 2, 2班
<==      Total: 1
2班
```

#### MyBatis注解开启二级缓存

```xml
<settings>
    <!-- MyBatis自带的缓存 使用第三方缓存时不用开启 开启二级缓存 -->
    <setting name="cacheEnabled" value="true"/>
</settings>
```

```java
//在相应的接口中开启注解
@CacheNamespace(blocking = true)
```
