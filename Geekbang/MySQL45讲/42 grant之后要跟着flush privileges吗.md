[TOC]

示例语句

```mysql
create user 'ua'@'%' identified by 'pa';
```

在 MySQL 里面,用户名 (user)+ 地址 (host) 才表示一个用户,因此 ua@ip1 和 ua@ip2 代表的是两个不同的用户

执行的操作

- 磁盘上,往 mysql.user 表里插入一行,由于没有指定权限,所以这行数据上所有表示权限的字段的值都是 N
- 内存里,往数组 acl_users 里插入一个 acl_user 对象,这个对象的 access 字段值为 0

## 全局权限

全局权限,作用于整个 MySQL 实例,这些权限信息保存在 mysql 库的 user 表里

```mysql
-- 给用户 ua 赋一个最高权限
grant all privileges on *.* to 'ua'@'%' with grant option;
```

执行的操作

- 磁盘上,将 mysql.user 表里,用户'ua'@'%'这一行的所有表示权限的字段的值都修改为'Y'
- 内存里,从数组 acl_users 中找到这个用户对应的对象,将 access 值(权限位)修改为二进制的"全 1"

所以对于新建的连接和已有的连接权限情况有所不同

- grant 命令对于全局权限,同时更新了磁盘和内存; 命令完成后即时生效,接下来新创建的连接会使用新的权限
- 对于一个已经存在的连接,它的全局权限不受 grant 命令的影响

总结:acl_users是全局变量,对所有的连接线程都可见,而对于全局权限,每个连接会复制一份到自己线程内存中,对其他的连接线程不可见,所以修改用户的全局权限后,不会影响到已经存在的连接的权限

```mysql
-- 回收上面的 grant 语句赋予的权限
revoke all privileges on *.* from 'ua'@'%';
```

执行的操作

- 磁盘上,将 mysql.user 表里,用户'ua'@'%'这一行的所有表示权限的字段的值都修改为"N"
- 内存里,从数组 acl_users 中找到这个用户对应的对象,将 access 的值修改为 0

## db 权限

除了全局权限,MySQL 也支持库级别的权限定义

```mysql
-- 让用户 ua 拥有库 db1 的所有权限
grant all privileges on db1.* to 'ua'@'%' with grant option;
```

基于库的权限记录保存在 mysql.db 表中,在内存里则保存在数组 acl_dbs 中

执行的操作

- 磁盘上,往 mysql.db 表中插入了一行记录,所有权限位字段设置为"Y"
- 内存里,增加一个对象到数组 acl_dbs 中,这个对象的权限位为"全 1"

每次需要判断一个用户对一个数据库读写权限的时候,都需要遍历一次 acl_dbs 数组,根据 user、host 和 db 找到匹配的对象,然后根据对象的权限位来判断

总结: acl_dbs 和 acl_users 一样,都是全局数组,所有线程判断 db 权限都用这个数组,所以当使用revoke 操作时,会立刻影响到后续其他会话权限的判断

有个特别的点需要注意下就是

- 如果当前会话已经处于某一个 db 里面,之前 use 这个库的时候拿到的库权限会保存在会话变量中,所以在切换出 db1 库之前,这个会话一直持有原来的权限

## 表权限和列权限

除了 db 级别的权限外,MySQL 支持更细粒度的表权限和列权限

- 表权限定义存放在表 mysql.tables_priv 中
- 列权限定义存放在表 mysql.columns_priv 中
- 这两类权限,组合起来存放在内存的 hash 结构 column_priv_hash 中

```mysql
create table db1.t1(id int, a int);
-- 赋予表权限
grant all privileges on db1.t1 to 'ua'@'%' with grant option;
-- 赋予列权限
GRANT SELECT(id), INSERT (id,a) ON mydb.mytbl TO 'ua'@'%' with grant option;
```

跟 db 权限类似,这两个权限每次 grant 的时候都会修改数据表,也会同步修改内存中的 hash 结构,会马上影响到已经存在的连接

## flush privileges

### 作用

flush privileges 命令会清空 acl_users 数组,然后从 mysql.user 表中读取数据重新加载,重新构造一个 acl_users 数组; 也就是说,以数据表中的数据为准,会将全局权限内存数组重新加载一遍(对于 db 权限、表权限和列权限同理)

结论

- 如果内存的权限数据和磁盘数据表相同的话,不需要执行 flush privileges; 而如果都是用 grant/revoke 语句来执行的话,内存和数据表本来就是保持同步更新的
- 因此,正常情况下,grant 命令之后,没有必要跟着执行 flush privileges 命令

### 使用场景

当数据表中的权限数据跟内存中的权限数据不一致的时候,flush privileges 语句可以用来重建内存数据,达到一致状态

- 直接用 DML 语句操作系统权限表

示例

```mysql
create user 'ua'@'%' identified by 'pa';
-- 直接删除了数据表的记录,而内存的数据还存在
delete from mysql.user where user='ua';
-- 失败 mysql.user 表中找不到这行记录
grant super on *.* to 'ua'@'%' with grant option;
-- 内存判断的时候,会认为这个用户还存在
create user 'ua'@'%' identified by 'pa';
```

## 总结

- grant 语句会同时修改数据表和内存,判断权限的时候使用的是内存数据,因此,规范地使用 grant 和 revoke 语句,是不需要随后加上 flush privileges 语句的
- flush privileges 语句本身会用数据表的数据重建一份内存权限数据,所以在权限数据可能存在不一致的情况下再使用(这种不一致往往是由于直接用 DML 语句操作系统权限表导致的,所以尽量不要使用这类语句)

