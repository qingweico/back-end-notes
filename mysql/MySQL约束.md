[TOC]

# MySQL约束

六大约束:

- NOT NULL　         非空　   保证包字段的不为为空
- DEFAULT                默认    同于保证该字段具有默认值
- PRIMARY KEY        主键    用于保证该字段的值具有唯一性,且非空
- UNIQUE                 唯一    用于保证该字段的值具有唯一性 可以为空
- CHECK                    检查约束（mysql不支持） 检查年龄 性别等等
- FOREIGN KEY        外键    用于限制两个表的关系,用于保证该字段的值必须来自于主表所关联列的值

-----

- zerofill                     零填充
- unsigned                无符号

添加约束的时间:

- 创建表时
- 修改表时

约束的添加分类:

- 表级约束 :  除了非空和默认其他都支持
- 列级约束 :  对外键约束没有效果

## 创建表时添加约束

```mysql
create TABLE Course(
Cno char(3) PRIMARY KEY,        # 列级约束   列级约束不可以自定义约束名称 主键默认的约束名为PRIMARY
Cname VARCHAR(20),
Cpno char(3),
Ccredit TINYINT,
FOREIGN key (Cpno) REFERENCES   # 表级约束    
Course(Cno)
CONSTRAINT fk_1 FOREIGN key (Cpno) REFERENCES Course(Cno) # 表级约束 自定义约束名
);
```

## 修改表时添加约束

```mysql
# 向字段中添加非空 字段的类型不可以省略 column可以省略
# 因为varchar没有默认值 所以修改varchar一定要加上长度
alter table studentinfo MODIFY (COLUMN) name varchar(5) not null;
```

```mysql
# 向字段中添加默认
alter table studentinfo MODIFY (COLUMN) sex char(11) DEFAULT '男';
```

```mysql
# 向字段中添加主键（列级方式）
alter table studentinfo MODIFY (COLUMN) name VARCHAR(5) PRIMARY key;
```

```mysql
# 向字段中添加主键 （表级方式）
alter table studentinfo add PRIMARY key();
```

```mysql
# 修改表时添加外键
ALTER TABLE ADD CONSTRAINT fk_1 FOREIGN KEY (Sno) REFERENCES student(Sno);
```

## 修改表时删除约束

```mysql
# 删除主键
alter table studentinfo DROP PRIMARY key;
```

```mysql
# 删除唯一
alter table studentinfo index (unique系统默认值);
# 添加唯一
alter table studentinfo add unique(字段);
```

```mysql
# 删除外键
ALTER TABLE SC DROP FOREIGN KEY fk_1;
```

```mysql
# 查询表的索引
show index from account;
```

```mysql
# 查看sql语句执行时的性能
# 根据id查询只需查询一行,而根据非主键（没有索引）查询需要遍历整个表
EXPLAIN select name from book WHERE id = 12;
```

##  键和唯一的区别

主键不可以为null 而唯一可以  但是一列中null也只能有一个

一个表中至多有一个主键 而唯一可以有多个

主键和唯一都可以进行组合 但是不推荐   `primary key(id,name,...);`

## 外键的要求

- 添加外键的列所在的表为从表 外键所引用的列所在的表为主表
- 添加外键的列与关联列的数据类型要一致或者兼容
- 所外键关联的列必须是一个key（一般是主键或者是唯一）
- 删表是应该先删从表再删主表
- 建表时应该先建主表再建从表

## 标识列

auto_increment  自增

- 标识列必须和key搭配 一般是主键
- 标识列所在的字段只可能是数值型
- 标识列至多有一个
- 可以通过`set auto_increment` 来设置自增的步长(`show VARIABLES like "%auto_increment%"`查看系统变量)

```mysql
# 添加自增长列
alter table account MODIFY COLUMN id int auto_increment;
#删除直接将auto_increment去掉即可
```
