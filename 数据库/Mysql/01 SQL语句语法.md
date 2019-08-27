数据库操作指令都由';'结尾，代表执行命令，不加分号换行表示继续写。

SQL语言(结构化查询语言)

```sql
SQL语言主要用于存取数据、查询数据、更新数据和管理关系数据库系统,SQL语言由IBM开发。SQL语言分为3种类型：
1、DDL语句    数据库定义语言： 数据库、表、视图、索引、存储过程，例如create drop alter
-- create  用于创建数据库，创建表，创建视图
-- drop    用于删除数据库，删除表，删除视图
-- alter    用于修改表的定义，修改视图的定义
2、DML语句    数据库操纵语言： 插入数据insert、删除数据delete、更新数据update、查询数据select
-- insert   用于插入数据
-- update 用于更新数据
-- delete  用于删除数据
-- select  用于查询数据
3、DCL语句    数据库控制语言： 例如控制用户的访问权限grant、revoke
-- grant   用于给用户增加权限
-- revoke 用于收回用户权限
```

## 库操作

查看当前用户下所有数据库名称

```sql
show databases;
```

查看数据库创建信息

```sql
show create database 表\G; -- \G可以纵向显示
```

查看当前位于那个数据库下

```sql
select database();
```

切换数据库

```sql
use db -- 切换到db数据库
```

创建数据库

```sql
create database db1 -- 创建一个名为db1的数据库，默认编码为utf8
-- 创建时设置编码,db1为新建库名
create database db1 default character set=utf8
```

删除数据库

```sql
drop database db -- 删除名为db的数据库，如果这个数据库不存在，就会报错
drop database if exists db-- 就算db不存在，也不会报错`
```

导出现有数据库数据：

```sql
mysqldump -u用户名 -p密码 数据库名称 >导出文件路径 --  结构+数据
mysqldump -u用户名 -p密码 -d 数据库名称 >导出文件路径   -- 结构
```

导入现有数据库数据

```sql
mysqldump -uroot -p密码  数据库名称 < 文件路径
```

修改数据库的字符集

```sql
ALTER DATABASE 库名 CHARACTER SET utf8 COLLATE utf8_general_ci;
```

## 表操作

建表

```sql
create table user(
    id int primary key AUTO_INCREMENT, 
    name char(10) not null, 
    sex char(3) not null
)ENGINE=MYISAM DEFAULT CHARSET=utf8;
```

查看表结构

```sql
desc 表名;
describe 表名;
show create table 表名; -- 查看表的创建信息, \G竖行显示
```

修改表名

```
alter table 表名 rename [as|to] 新表名;
```

增加字段

```sql
alter table 表名 add 字段名 数据类型 约束条件, add 字段名 数据类型 约束条件; -- 添加多列逗号隔开
alter table 表名 add 字段名 数据类型 约束条件 first; -- 添加在开头
alter table 表名 add 字段1 数据类型 约束条件 after 字段2; -- 将字段1 加在字段2后
```

删除字段

```sql
alter table 表名 drop 字段名;
```

修改字段

```sql
alter table 表名 modify 字段名 数据类型 约束条件; -- 可以用来修改数据类型和约束条件，要写全
--列名称修改,也可以用来修改约束条件
alter table 表名 change 原列名 新列名 类型 [约束条件];
-- 给字段增加约束条件
alter table 表名 alter column 字段 set 约束条件;
```

删除表

```sql
drop table 表名;
```

修改表的搜索引擎

```sql
alter table '表名' engine = 引擎
```

 拷贝表

```sql
create table 表名 select * from 库.表;-- 创建表的时候使用另一个表的数据
-- 只拷贝表的结构,不拷贝数据
create table 表名 select * from 库.表 where 1=2; -- 设置一个不成立的条件,查不到数据
```

## 数据操作

添加数据

```sql
insert into 表名(列名,多个列名逗号隔开) values('值','与列名一一对应'),('可以一次添加多行','与之前逗号隔开')
```

删除数据

```sql
delete from 表名 where 条件 and 条件 -- 会删除所有满足条件的行
```

清空数据

```sql
delete from 表名 truncate table 表名 
-- 区别
-- 不带where参数的delete语句可以删除mysql表中所有内容，使用truncate table也可以清空mysql表中所有内容。
-- 效率上truncate比delete快，但truncate删除后不记录mysql日志，不可以恢复数据。
-- delete的效果有点像将mysql表中所有记录一条一条删除到删完，
-- 而truncate相当于保留mysql表的结构，重新创建了这个表，所有的状态都相当于新表。
```

修改数据

```sql
update 表名 set 列名=新的值 where 条件 -- 多个条件用and连接`
```

查表

```sql
select 要查的列名,逗号隔开，*代表所有列 from 表名 -- 查全部
-- 还可以根据条件查
select 要查的列名,逗号隔开，*代表所有列 from 表名 where 条件
```

