---
layout: post
title: MySQL 数据表的基本操作
categories: [MySQL]
description: some word here
keywords: MySQL
---

# 数据表的基本操作

创建并选中 itcast 数据库
```sql
mysql> CREATE DATABASE itcast;
Query OK, 1 row affected (0.00 sec)

mysql> USE itcast
Database changed

mysql> CREATE TABLE tb_grade(
    id CHAR(5),
    name VARCHAR(20),
    score FLOAT
);
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW TABLES;
+------------------+
| Tables_in_itcast |
+------------------+
| tb_grade         |
+------------------+
2 rows in set (0.00 sec)

mysql > SHOW CREATE TABLE tb_grade \G
*************************** 1. row ***************************
       Table: tb_grade
Create Table: CREATE TABLE `tb_grade` (
  `id` char(5) DEFAULT NULL,
  `name` varchar(20) DEFAULT NULL,
  `score` float DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

mysql > INSERT INTO tb_grade
    VALUE('11111','Tony',91),
         ('22222','Steve',88.5),
         ('33333','George',76),
         ('44444','Mary',61);
Query OK, 4 rows affected (0.00 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM tb_grade;
+-------+--------+-------+
| id    | name   | score |
+-------+--------+-------+
| 11111 | Tony   |    91 |
| 22222 | Steve  |  88.5 |
| 33333 | George |    76 |
| 44444 | Mary   |    61 |
+-------+--------+-------+
4 rows in set (0.00 sec)
```