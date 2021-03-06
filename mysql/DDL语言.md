## DDL

### 修改表

```mysql
alter TABLE userinfo CHANGE COLUMN username name INT; #将表userinfo中字段为username修改为name,且类型修改为int
```

```mysql
ALTER TABLE userinfo ADD COLUMN age int;              #向表中增加字段
```

```mysql
ALTER TABLE userinfo DROP COLUMN age;                 #删除表中的字段
```

```mysql
ALTER TABLE userinfo RENAME to userinfos;             #修改表名
```

```mysql
ALTER TABLE userinfos MODIFY name int;                #修改表中字段的类型
```

### 表的删除

```mysql
drop table 表名;                                      #从数据库中删除该表,会改变表的数据结构
```

### 表的复制

```mysql
create TABLE info like userinfos;                    #只复制表的结构,不会复制表的内容
```

```mysql
create TABLE info SELECT * from userinfos;           #复制表的结构+表的内容
```



