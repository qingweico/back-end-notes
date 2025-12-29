[TOC]

# DML

## 插入语句

```mysql
# 方式二
insert into account(username,password) select 用户1,001 UNION ALL select 用户二,002;
```

## 删除语句

```mysql
delete from 表；          # 删除表中所有的数据
```

```mysql
delete from 表 where 条件 # 删除表中满足条件的行
```

```mysql
truncate table 表;        # 删除表中所有的数据
```

1和3的区别是,前者删除后自增属性会从断点处继续自增,而后者则会重新开始

1的效率没有3高

1可以使用条件筛选,而3则不行

## 修改语句

## 查看表结构语句

```sql
DESCRIBE t_user;
SHOW COLUMNS
FROM
	t_user;
SHOW CREATE TABLE t_user;
SELECT
    COLUMN_NAME AS '字段名',
    COLUMN_TYPE AS '数据类型',
    IS_NULLABLE AS '是否为空',
    COLUMN_DEFAULT AS '默认值',
    COLUMN_COMMENT AS '字段说明'
FROM
    INFORMATION_SCHEMA.COLUMNS
WHERE
    TABLE_SCHEMA = 'test'
  AND TABLE_NAME = 't_user' SELECT
	TABLE_NAME,
	COLUMN_NAME,
	CONSTRAINT_NAME,
	REFERENCED_TABLE_NAME,
	REFERENCED_COLUMN_NAME
FROM
    INFORMATION_SCHEMA.KEY_COLUMN_USAGE
WHERE
    TABLE_SCHEMA = 'test'
  AND TABLE_NAME = 't_user';

```
