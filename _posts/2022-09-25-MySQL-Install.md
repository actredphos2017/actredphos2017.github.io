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
在服务器内输入 `STATUS;` 便可查看服务器的详细信息
```sql
mysql> STATUS;
--------------
mysql  Ver 8.0.30 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          8
Current database:
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
Uptime:                 14 sec

Threads: 2  Questions: 5  Slow queries: 0  Opens: 119  Flush tables: 3  Open tables: 38  Queries per second avg: 0.357
--------------
```