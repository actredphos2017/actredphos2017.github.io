---
layout: post
title: MySQL 的安装与基础操作
categories: [MySQL]
description: some word here
keywords: MySQL, Ubuntu
---

# MySQL 的安装

## MySQL for Ubuntu 的安装

在 [MySQL Community Downloads](https://dev.mysql.com/downloads/mysql/) 网站找到适合的版本下载

在 Ubuntu 下进行安装
```shell
[root@localhost]~#

dpkg -i **.deb

apt update && apt upgrade

apt upgrade mysql*

mysqld

mysql
```

**mysqld 为服务端, mysql 为客户端**

## 常用与排障命令

```shell
[root@localhost]~#

service mysql start
//启动mysql后台服务

service mysql restart
//重启mysql后台服务

service mysql stop
//关闭mysql后台服务

mysqld --user=root
//强制以root用户启动服务器

ps -ef | grep mysql
//查看mysql运行状态

mysqladmin shutdown
//关闭mysql服务器
```

## 操作命令

```shell
[root@localhost]~#

mysql_secre_installation
//设置用户密码

mysql -h [ip] -u [Username] -p [Password]
//登录
```

# MySQL 基本命令

用 `STATUS` 或 `\s` 查询数据库消息

```sql
mysql> STATUS
--------------
mysql  Ver 8.0.30 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          8
Current database:       iscast
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.30 MySQL Community Server - GPL
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    utf8mb4
Conn.  characterset:    utf8mb4
UNIX socket:            /var/run/mysqld/mysqld.sock
Binary data as:         Hexadecimal
Uptime:                 2 hours 49 min 5 sec

Threads: 2  Questions: 33  Slow queries: 0  Opens: 147  Flush tables: 3  Open tables: 66  Queries per second avg: 0.003
--------------

```
用 `CREATE DATABASE` 来创建数据库

```sql
mysql> CREATE DATABASE test;
Query OK, 1 row affected (0.01 sec)
```

用 `SHOW DATABASES` 来显示已经创建的数据库

```sql
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
6 rows in set (0.00 sec)
```

用 `SHOW CREATE DATABASE` 来显示已创建的数据库信息

```sql
mysql> SHOW CREATE DATABASE test;
+----------+--------------------------------------------------------------------------------------------------------------------------------+
| Database | Create Database                                                                                                                |
+----------+--------------------------------------------------------------------------------------------------------------------------------+
| test     | CREATE DATABASE `test` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */ |
+----------+--------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

用 `ALTER` 修改数据库

```sql
mysql> ALTER DATABASE test DEFAULT CHARACTER SET gbk COLLATE gbk_bin;
Query OK, 1 row affected (0.00 sec)

mysql> SHOW CREATE DATABASE test;
+----------+-----------------------------------------------------------------------------------------------------------------+
| Database | Create Database                                                                                                 |
+----------+-----------------------------------------------------------------------------------------------------------------+
| test     | CREATE DATABASE `test` /*!40100 DEFAULT CHARACTER SET gbk COLLATE gbk_bin */ /*!80016 DEFAULT ENCRYPTION='N' */ |
+----------+-----------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

用 `DROP DATABASE` 来删除已经创建的数据库

```sql
mysql> DROP DATABASE test;
Query OK, 0 rows affected (0.01 sec)
```

用 `USE` 或者 `\u` 来切换选择的数据库

```sql
mysql> CREATE DATABASE itcast;
Query OK, 1 row affected (0.00 sec)

mysql> \u itcast
Database changed
```