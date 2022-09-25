---
layout: post
title: MySQL 总结
categories: [MySQL]
description: some word here
keywords: MySQL
---

## DDL 总结

```sql
mysql> STATUS; --服务器信息
mysql> SHOW DATABASES; --查询所有数据库
mysql> SELECT DATABASE(); --查询当前数据库
mysql> CREATE DATABASE (IF NOT EXISTS) [数据库名] (DEFAULT CHARSET [字符集]) (COLLATE [排序规则]); --创建数据库
mysql> DROP DATABASE (IF EXISTS) [数据库名]; --删除数据库
mysql> USE [数据库名]; --使用数据库

mysql> SHOW TABLES; --查询当前数据库中的所有表
mysql> DEC [表名]; --查询表结构
mysql> SHOW CREATE TABLE [表名]; --查询指定表的建表语句

mysql> CREATE TABLE [表名](
  [字段1名] [数据类型] (COMMENT [字段1显示名]),
  [字段2名] [数据类型] (COMMENT [字段2显示名]),
  [字段3名] [数据类型] (COMMENT [字段3显示名]),
  ...
  [字段N名] [数据类型] (COMMENT [字段N显示名])
) (COMMIT 表的显示名); --表的创建

mysql> ALTER TABLE [表名] [修改方法] --修改表

常用修改方法:
    ADD [新字段名] [数据类型] (COMMENT 新字段显示名) (约束); --添加新的字段

    MODIFY [字段名] [新的数据类型]; --修改数据类型

    CHANCE [Old 字段名] [新的字段名] [数据类型] (COMMENT [新的字段显示名]); --修改字段名和数据类型

    DROP [字段名]; --删除字段名

    RENAME IO [新的表名] --修改表名

mysql> TRUNCATE TABLE [表名] --重构表
```

---

## DML 总结

```sql
mysql> INSERT INTO [表名](字段1, 字段2, ...) VALUES(值1, 值2, ...); --给指定字段添加数据
mysql> INSERT INTO [表名] VALUES(值1, 值2, ...); --给全部字段添加数据

mysql> INSERT INTO [表名](字段1, 字段2, ...) VALUES(值1, 值2, ...),(值1, 值2, ...)...;
mysql> INSERT INTO [表名] VALUES(值1, 值2, ...),(值1, 值2, ...)...;
/*批量添加数据*/

mysql> UPDATE [表名] SET 字段1 = 值1, 字段2 = 值2, ... (WHERE [条件]); --更新元素
mysql> DELETE FROM [表名] (WHERE [条件]); --删除元素
```