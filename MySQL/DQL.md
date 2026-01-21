# 目录

[TOC]

## MySQ DQL语言

### 基本语句

- show database(); 用来展示所有的数据库 
- use 库名; 进入库
- show tables; 展示所有的表
- show tables from 库名;  查看库名中的所有的表(并没有进入该库)
- select database();  查看当前位置在哪个库
- create database 库名;  创建一个新的数据库
- create table 表名(
  -  -> name varchar(20), #回车 
  -  -> age   int );                #回车     创建一个student的表  
- insert into 表名(key,key) value (值,值);  向表中添加新的数据 
- update 表名 set value=... where key=...; 修改表中的数据,可以根据键值来修改值,反之亦可 
- delete from 表名 where key=...; 删除key所在的一行   
- desc 表名;    展示表的结构 
- drop   database/table  库名/表名;     删除一个库或者表

-----

MYSQL单行注释为# 或--- 多行注释为/*  */

#### MySQL分为5种语言

-   DQL语言(数据查询语言)  Data Query Language                     查询语句
-   DML语言(数据操作语言)  Data Manipulation Language        cud语句
-   DDL语言(数据描述语言)  Data Definition  Language              create drop alter 对表结构的cud
-   TCL语言(事物控制语言)  Transaction Controller Language   事物的提交和回滚
-   DCL(数据控制语言)  Data Comtrol Language                          grant授权 和revoke撤销授权

-------------

#### crud  语句 

-  增:  insert into 表名(key,key....) values (value,value.......) ; 当插入多行数据 也可以省略键名  
-  删: delete from 表名 where  key =....  ;                                 删除key所在的一行 
-  改:update 表名  set value=... where key =.... ;                    根据值来修改键 当然而也可以根据键来修改值  
-  查:select * from 表名 where .....  ;                                        可以查看表中的任意值  

***select version(); 查看当前MYSQL的版本 或者进入dos窗口输入mysql --version 或mysql -V*** 

***MySQL是不区分大小写的 建议关键字大写,做到语法规范  每条命令最好以分号结尾*** 

#### 起别名 

```mysql
select 100 (as) name;
```

#### 去重

```mysql
SELECT DISTINCT age from job;
```

#### mysql中加号的作用

- **在mysql中加号仅仅只作为运算符,并没有连接两个字符串的作用**
- **连接两个及以上的字符串只能使用concat() 函数**

```mysql
SELECT CONCAT(username,"----",password) as UserInfo FROM UserInfo;
```

### 条件查询

- 按条件表达式筛选
  - 条件运算符 : <  > ***<>*** = != <= >=
- 按逻辑表达式筛选 
  - 逻辑运算符: && || !  ***and or not***
- 模糊查询
  - like ,between and , is null ,in	

```MYSQL
SELECT * FROM job WHERE NOT(sex ="女") or salary>10000;
```

Like 查询 通常与通配符一块使用

- %  匹配多个字符
- _   匹配单个字符

```mysql
SELECT * FROM job where age LIKE '%4'; 
```

```mysql
SELECT * FROM job where name LIKE "%L%";
```

当匹配的字符中含有通配符时应该使用转义字符 也可以自定义转义字符

```mysql
SELECT * FROM job WHERE age LIKE "$_4" ESCAPE '$';   #定义$为转义字符
```

between and

```mysql
SELECT * FROM job where salary BETWEEN 10000 AND 12000;# 包括左端点和右端点
```

注意10000和12000之间的顺序不能颠调

IN

```mysql
SELECT * from job where age IN(21,24);
```

is (not) NULL

```mysql
SELECT * from job WHERE salary is null;
```

IFNULL

```mysql
SELECT (IFNULL(salary,0)) as "年薪" from job; #假如薪水为NULL 则变为0
```

### 排序查询

```mysql
SELECT * from job ORDER BY salary (ASC);  #升序排列     默认的是升序排列,ASC可以省略
SELECT * from job ORDER BY salary DESC;   #降序排列
```

注意这里的salary的类型是int类型,如果是字符类型的话会按照字典排序

```mysql
SELECT name,salary*12 as 年薪 from job ORDER BY 年薪;
```

```mysql
SELECT LENGTH(name) as 姓名字节长度,age from job ORDER BY 姓名字节长度;
```

```mysql
SELECT * from job ORDER BY salary,age DESC;  #按薪水升序排列; 相同的薪水按照年龄降序排列
```

### 常见函数

- 字符函数 `length`,`concat`,`upper`,`substr`,`LPAD`,`RPAD`,`replace`,`trim`

  ```mysql
  SELECT SUBSTR("username",5) 结果;
  ```

  在SQL语句中substr截取字符的索引是从1开始的

  length函数读取的是字节数,而substr截取的字符数

  ```mysql
SELECT INSTR("hello world","world") 结果;
  ```
  
  ***instr函数返回子串第一次出现的位置相当于indexOf 不存在则返回0***

  ```mysql
select LPAD("I am in the house",20,'-');  /左填充
  ```
  
  ```mysql
SELECT REPLACE("learn mysql","mysql","java");
  ```

  ```mysql
  SELECT TRIM("abc" FROM "abcabcabcabc数abc据库abc") 结果;
  ```

- 数学函数 `round`,`ceil`,`floor`,`mod`,`truncate(截断)`

- 日期函数

  - NOW() 返回系统日期-时间
  - CURDATE() 返回系统的日期
  - CURTIME() 返回系统的时间
  - STR_TO_DATE(str,format);   日期格式化 相当于java中format函数 序列化日期
  - DATE_FORMAT(date,format); 将字符串类型转化为日期类型 相当于java中的parse函数 反序列化日期 
  
- 其他函数  USER() DATABASE() VERSION()......

- 流程控制函数  

  - ```mysql
    SELECT salary as 原工资,age,CASE age
    WHEN 22 THEN salary*1.5    #查询员工的年龄和当前薪水
    WHEN 23 THEN salary*2      #年龄为22时当前薪水*1.5
    WHEN 24 then salary*3      #年龄为23时薪水*2 
    END as 新工资 FROM job;     #年龄为24时薪水*3
    ```

    ![1584274392635](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/1584274392635.png)

  - ```mysql
    SELECT *, CASE
    WHEN salary > 14000 THEN 'A'   #根据员工的薪水显示员工的工资等级
    WHEN salary > 12000 THEN 'B'
    WHEN salary > 10000 THEN 'C'
    ELSE 'D'
    END as 工资级别 FROM job;
    ```

#### 聚合函数

- sum  求和
- avg  平均值
- max 最大
- min 最小

```mysql
select sum(salary)as 和, MAX(salary) 最大, AVG(salary) 平均 from job;
```

上面4种函数都不会计算null

#### 分组函数与DISTINCT一起的使用

```mysql
select count(DISTINCT salary) 工资的种类,count(salary) 工资种数 from job;
# avg max min都有同样的用法
```

```mysql
SELECT COUNT(*) 行数 FROM job;
```

### 分组查询

```mysql
SELECT MAX(salary) 最高工资, sex from job GROUP BY sex; # 查询男员工和女员工的最高工资
```

#### 筛选后分组 

***关键字where 放在group by 之前***

```mysql
select COUNT(*),age,sex from job where name LIKE "%a%" GROUP BY sex;
# 查询姓名中包含字母a的男女员工各有几人
```

#### 分组后筛选

关键字having 放在group by 之后 也可以添加order by 语句 要放在最后面 也就是group by之后

```mysql
select count(*) ,age from job group BY age having COUNT(*) >2;
# 查询年龄相同且个数大于2的年龄
```

|            | 关键字 | 筛选的表   | 位置            |
| ---------- | ------ | ---------- | --------------- |
| 分组前筛选 | where  | 原始表     | group by 的前面 |
| 分组后筛选 | having | 分组后的表 | group by 的后面 |

#### 分组后筛选再分组

```mysql
SELECT COUNT(*),MIN(salary),sex from job where `name` REGEXP '[^c]' GROUP BY sex having MIN(salary)>4000;
# 查询姓名中不包含字母c的男女员工各自最低工资大于4000的员工
```

### 连接查询

#### 按年代分

- sql92标准
- sql99标准

#### 按功能分类

- 内连接
  - 等值连接
  - 非等值连接
  - 自连接
- 外连接
  - 左外连接
  - 右外连接
  - 全外连接
- 交叉连接

#### 等值连接 (取两表及两表以上交集的部分)(sql92)

```mysql
SELECT country,employee_tbl.`name` from employee_tbl,websites WHERE                employee_tbl.id=websites.id; //如果两张表都含有相同的字段的话需要加表名限制                       
```

#### 内连接(sql99)

```mysql
select s.*,c.* from student s INNER JOIN class c on s.number = c.cid;
# 查询学生的班级信息
```

#### 左外连接(sql99)

```mysql
select s.*,c.* from student s left OUTER JOIN class c on s.number  = c.cid WHERE c.cid is NULL;  #查询没有班级的学生
```

left关键字左边的是主表 而右边的是丛表 左外连接就是内连接的结果加上主表有而从表没有的结果

#### 右外连接(sql99)

```mysql
select s.*,c.* from class c  LEFT outer JOIN student s on c.cid = s.number WHERE s.number is NULL; #查询没有学生的班级
select s.*,c.* from student s  right outer JOIN class c  on c.cid = s.number WHERE s.number is NULL;
```

right关键字右边的是主表 而左边的是丛表 👉外连接就是内连接的结果加上主表有而从表没有的结果

### 子查询

```mysql
# 按出现的位置
select 后面
from 后面
where或having后面
# 按结果集的行和列不同
 标量子查询  一行一列        > < <> =
 列子查询    一列多行       in  any some all
 行子查询    一行多列
 表子查询    多列多行
 exists(相关子查询,一般exists后面就是连接查询)
```

### 分页查询

```mysql
# 查询前10条学生的信息
select * from student LIMIT 0,10;
```

```mysql
select * from student limit (page-1)*size,size;
```

### 联合查询

```mysql
# 两条语句之间的关系是or
select * from job where salary>6000
UNION
select * from job where name LIKE '%j%';
#UNION ALL 去重
```

联合语句多用来查询多表之间具有相同类型的列,且查询的多表的列数要保持一致