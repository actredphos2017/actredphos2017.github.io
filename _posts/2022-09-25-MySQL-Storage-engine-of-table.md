---
layout: post
title: MySQL 数据表的存储引擎
categories: [MySQL]
description: some word here
keywords: MySQL
---

# 查询服务器可用的数据引擎

用 `SHOW ENGINES` 来查询当前服务器可用的数据引擎
```sql
mysql> SHOW ENGINES \G
*************************** 1. row ***************************
      Engine: ARCHIVE
     Support: YES
     Comment: Archive storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 2. row ***************************
      Engine: BLACKHOLE
     Support: YES
     Comment: /dev/null storage engine (anything you write to it disappears)
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 3. row ***************************
      Engine: MRG_MYISAM
     Support: YES
     Comment: Collection of identical MyISAM tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 4. row ***************************
      Engine: FEDERATED
     Support: NO
     Comment: Federated MySQL storage engine
Transactions: NULL
          XA: NULL
  Savepoints: NULL
*************************** 5. row ***************************
      Engine: MyISAM
     Support: YES
     Comment: MyISAM storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 6. row ***************************
      Engine: PERFORMANCE_SCHEMA
     Support: YES
     Comment: Performance Schema
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 7. row ***************************
      Engine: InnoDB
     Support: DEFAULT
     Comment: Supports transactions, row-level locking, and foreign keys
Transactions: YES
          XA: YES
  Savepoints: YES
*************************** 8. row ***************************
      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 9. row ***************************
      Engine: CSV
     Support: YES
     Comment: CSV storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
9 rows in set (0.00 sec)
```

# 各个引擎的特性

## MyISAM
MyISAM是MySQL的默认引擎，访问速度快，但不支持事务、也不支持外键.

每个MyISAM在磁盘上存储成3个文件,其文件名都和表名相同,扩展名分别是:
- .frm (存储表定义)
- .MYD (MYData,存储数据)
- .MYI (MYIndex,存储索引)

支持3种不同的存储格式:
- 静态表 (默认)
- 动态表
- 压缩表

```sql
mysql> create table Myisam_char(name char(10)) engine = myisam;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into Myisam_char values('abcde'),('abcde'),('abcde'),('abcde');
Query OK, 4 rows affected (0.00 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> select name,length(name) from Myisam_char;
+-------+--------------+
| name  | length(name) |
+-------+--------------+
| abcde |            5 |
| abcde |            5 |
| abcde |            5 |
| abcde |            5 |
+-------+--------------+
4 rows in set (0.00 sec)
```
---
## InnoDB
InnoDB存储引擎提供了具有提交、回滚和崩溃恢复能力的事务安全.但是对比MyISAM的存储引擎,InnoDB写的处理效率差一些, 并且会占用更多的磁盘空间以保留数据和索引.

```sql
mysql> create table autoincre_demo(
    i smallint not null auto_increment,
    name varchar(10),
    primary key(i)
)engine = innodb;
Query OK, 0 rows affected (0.02 sec)

mysql> insert into autoincre_demo values(1,'1'),(0,'2'),(null,'3');
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> select * from autoincre_demo;
+---+------+
| i | name |
+---+------+
| 1 | 1    |
| 2 | 2    |
| 3 | 3    |
+---+------+
3 rows in set (0.01 sec)
```
可以通过 `ALTER TABLE [Table Name] AUTO_INCREMENT = n;` 语句强制设置自动增长列的初始值，默认从1开始，但是该强制的默认值是保留在内存中的，如果该值在使用之前数据库重新启动，那么这个强制的默认值就会丢失，就需要在数据库启动以后重新设置。

可以使用 `LAST_INSERT_ID()` 查询当前线程最后插入记录使用的值。如果一次插入了多条记录，那么返回的是第一条记录使用的自动增长值。

对于 InnoDB 表，自动增长列必须是索引。如果是组合索引，也必须是组合索引的第一列，但是对于 MyISAM 表，自动增长列可以是组合索引的其他列，这样插入记录后，自动增长列是按照组合索引的前面几列进行排序后递增的。

---
## MEMORY
MEMORY 存储引擎使用存在于内存中的内容来创建表。每个MEMORY表只实际对应一个磁盘文件，格式是.frm。MEMORY类型的表访问非常地快，因为它的数据是放在内存中的，并且默认使用HASH索引，但是一旦服务关闭，表中的数据就会丢失掉。
```sql
mysql> CREATE TABLE tab_memory ENGINE=MEMORY
    SELECT city_id,city,country_id
    FROM city GROUP BY city_id;
Query OK, 600 rows affected (0.06 sec)
Records: 600 Duplicates: 0 Warnings: 0
mysql> select count(*) from tab_memory;
+----------+
| count(*) |
+----------+
| 600 |
+----------+
1 row in set (0.00 sec)
mysql> show table status like 'tab_memory' \G
*************************** 1. row ***************************
Name: tab_memory
Engine: MEMORY
Version: 10
Row_format: Fixed
Rows: 600
Avg_row_length: 155
Data_length: 127040
Max_data_length: 16252835
Index_length: 0
Data_free: 0
Auto_increment: NULL
Create_time: NULL
Update_time: NULL
Check_time: NULL
Collation: gbk_chinese_ci
Checksum: NULL
Create_options:
Comment:
1 row in set (0.00 sec)
```

---
## MERGE
MERGE存储引擎是一组MyISAM表的组合，这些MyISAM表必须结构完全相同，MERGE表本身并没有数据，对MERGE类型的表可以进行查询、更新、删除操作，这些操作实际上是对内部的MyISAM表进行的。对于MERGE类型表的插入操作，是通过INSERT_METHOD子句定义插入的表，可以有3个不同的值，使用FIRST或LAST值使得插入操作被相应地作用在第一或最后一个表上，不定义这个子句或者定义为NO，表示不能对这个MERGE表执行插入操作。

可以对MERGE表进行DROP操作，这个操作只是删除MERGE的定义，对内部的表没有任何的影响。

MERGE表在磁盘上保留两个文件，文件名以表的名字开始，一个.frm文件存储表定义，另一个.MRG文件包含组合表的信息，包括MERGE表由哪些表组成、插入新的数据时的依据。可以通过修改.MRG文件来修改MERGE表，但是修改后要通过FLUSH TABLES刷新。
```sql
mysql> create table payment_2006(
-> country_id smallint,
-> payment_date datetime,
-> amount DECIMAL(15,2),
-> KEY idx_fk_country_id (country_id)
-> )engine=myisam;
Query OK, 0 rows affected (0.03 sec)

mysql> create table payment_2007(
-> country_id smallint,
-> payment_date datetime,
-> amount DECIMAL(15,2),
-> KEY idx_fk_country_id (country_id)
-> )engine=myisam;
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE payment_all(
-> country_id smallint,
-> payment_date datetime,
-> amount DECIMAL(15,2),
-> INDEX(country_id)
-> )engine=merge union=(payment_2006,payment_2007) INSERT_METHOD=LAST;
Query OK, 0 rows affected (0.04 sec)

mysql> insert into payment_2006 values(1,'2006-05-01',100000),(2, '2006-08-15',150000);
Query OK, 2 rows affected (0.00 sec)
Records: 2 Duplicates: 0 Warnings: 0

mysql> insert into payment_2007 values(1, '2007-02-20',35000),(2, '2007-07-15', 220000);
Query OK, 2 rows affected (0.00 sec)
Records: 2 Duplicates: 0 Warnings: 0

mysql> select * from payment_2006;
+------------+---------------------+-----------+
| country_id | payment_date | amount |
+------------+---------------------+-----------+
| 1 | 2006-05-01 00:00:00 | 100000.00 |
| 2 | 2006-08-15 00:00:00 | 150000.00 |
+------------+---------------------+-----------+
2 rows in set (0.00 sec)

mysql> select * from payment_2007;
+------------+---------------------+-----------+
| country_id | payment_date | amount |
+------------+---------------------+-----------+
| 1 | 2007-02-20 00:00:00 | 35000.00 |
| 2 | 2007-07-15 00:00:00 | 220000.00 |
+------------+---------------------+-----------+
2 rows in set (0.00 sec)

mysql> select * from payment_all;
+------------+---------------------+-----------+
| country_id | payment_date | amount |
+------------+---------------------+-----------+
| 1 | 2006-05-01 00:00:00 | 100000.00 |
| 2 | 2006-08-15 00:00:00 | 150000.00 |
| 1 | 2007-02-20 00:00:00 | 35000.00 |
| 2 | 2007-07-15 00:00:00 | 220000.00 |
+------------+---------------------+-----------+
4 rows in set (0.00 sec)
```

---
## 总结：如何选择合适的存储引擎
在选择存储引擎时，应根据应用特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。
下面是几种常用存储引擎的适用环境。

MyISAM：默认的MySQL插件式存储引擎。如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不是很高，那么选择这个存储引擎是非常适合的。MyISAM是在Web、数据仓储和其他应用环境下最常使用的存储引擎之一。

InnoDB：用于事务处理应用程序，支持外键。如果应用对事务的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询以外，还包括很多的更新、删除操作，那么InnoDB存储引擎应该是比较合适的选择。InnoDB存储引擎除了有效地降低由于删除和更新导致的锁定，还可以确保事务的完整提交（Commit）和回滚（Rollback），对于类似计费系统或者财务系统等对数据准确性要求比较高的系统，InnoDB都是合适的选择。

MEMORY：将所有数据保存在 RAM 中，在需要快速定位记录和其他类似数据的环境下，可提供极快的访问。MEMORY 的缺陷是对表的大小有限制，太大的表无法缓存在内存中，其次是要确保表的数据可以恢复，数据库异常终止后表中的数据是可以恢复的。MEMORY表通常用于更新不太频繁的小表，用以快速得到访问结果。

MERGE：用于将一系列等同的MyISAM表以逻辑方式组合在一起，并作为一个对象引用它们。MERGE表的优点在于可以突破对单个MyISAM表大小的限制，并且通过将不同的表分布在多个磁盘上，可以有效地改善MERGE表的访问效率。这对于诸如数据仓储等VLDB环境十分适合。

*注意：以上只是我们按照实施经验提出的关于存储引擎选择的一些建议，但是不同应用的特点是千差万别的，选择使用哪种存储引擎才是最佳方案也不是绝对的，这需要根据用户各自的应用进行测试，从而得到最适合自己的结果。*

---

*本文参考图书《深入浅出MySQL“数据库开发、优化与管理维护（第2版）》*