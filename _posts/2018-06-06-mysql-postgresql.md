---
layout: post
title:  "PostgreSQL 与 MySQL 用法比较"
date:   2018-06-06 12:29:00 +0800
categories: MySQL 
tag: ['MySQL','PostgreSQL']
---

* content
{:toc}

---
| MySQL | PostgreSQL |  说明|
|---|---|---|
| 3306 | 5432|端口|
| mysqldump | pg_dumpall |备份：<br> mysqldump –all-databases > file.sql </br>pg_dumpall > /path/to/file.sql|
| mysql | psql |命令行|
| show databases; | \l or \list |数据库列表|
| use database; | \c [dbname] |选择数据库|
| show processlist; | `SELECT * FROM pg_stat_activity ` |查看活动线程|
| kill 17731; | `SELECT pg_terminate_backend(17731)`  |kill活动线程|
| `select date_format('2018-08-09 00:00:00', '%Y%m%d %H%i%s')` | `SELECT to_char(current_timestamp, 'yyyy-mm-dd HH24:MI:SS')`  |时间格式化|
|`select adddate('20180831',1);` | select date '20180831' +integer '1'|时间|
