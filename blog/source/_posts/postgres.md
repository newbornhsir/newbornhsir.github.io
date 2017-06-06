---
title: postgres
date: 2017-06-05 18:30:46
tags: postgresql
---

# postgresql学习
## 安装
下载文件安装（百度）
## 命令
1 建数据库语句(在postgresql 的sql shell中执行)

 `create database name;`，需要分号来结束语句，语句执行完后显示`CREATE DATABASE`则建表成功

2 查看数据库

`\l`

3 连接或退出数据库

连接`\c 数据库name`，退出`\q`

4 删除数据库

`DROP DATABASE NAME`这里我使用大写，发现小写创建的数据表删除了，所以postgresql应该是不区分大小写的

5 建表

 - 首先连接数据库`\c name`

 - 	建表语句
 	```
	CREATE TABLE table_name(
	   column1 datatype,
	   column2 datatype,
	   column3 datatype,
	   .....
	   columnN datatype,
	   PRIMARY KEY( one or more columns )
	); 

	```

6 查看和删除

`\d` `DROP TABLE table_name;`

7 schema

- 默认mydb数据库中只有一个架构public
- 执行 `create schema myschema;`命令后，架构中多了一个myschema，执行`create table table_name();`语句，是在public下建立数据表，在schema模式下建表需要执行 `create table schemaName.tableName();`
- 删除schema `drop schema schemaName`

8 insert into

- 插入

`insert into tableName(column1,column2,...,columnn) values(value1),...,(valuen);`

插入一行或者多行，返回数据`insert 0 rows`则插入成功

- 查看插入结果

`select * from tableName`，`select key1,key2,...,keyn from tableName`

9 运算符，主要用来条件查找,主要有算术、比较、逻辑、位串操作符

- `select * from tablename where (a-1=1)is true;`,
- `select * from tablename where id>1 and id<5;`,

10 update 和delete

- `update tablename 键值对（或多个） where 条件`
- `delete from tablename where 条件`

11 like模糊查询

    ```
     WHERE SALARY::text LIKE '200%'	Finds any values that start with 200

	 WHERE SALARY::text LIKE '%200%'	Finds any values that have 200 in any position
 
	 WHERE SALARY::text LIKE '_00%'	Finds any values that have 00 in the second and third positions

	 WHERE SALARY::text LIKE '2_%_%'	Finds any values that start with 2 and are at least 3 characters in length

	 WHERE SALARY::text LIKE '%2'	Finds any values that end with 2

	 WHERE SALARY::text LIKE '_2%3'	Finds any values that have a 2 in the second position and end with a 3

	 WHERE SALARY::text LIKE '2___3'	Finds any values in a five-digit number that start with 2 and end with 3

	```

12 orderby

`SELECT column-list FROM table_name [WHERE condition] [ORDER BY column1, column2, .. columnN] [ASC | DESC];`  

13 with


14 distinct

