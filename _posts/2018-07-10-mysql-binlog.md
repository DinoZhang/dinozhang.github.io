---
layout: post
title:  "MySQL Binlog"
date:   2018-07-10 12:29:00 +0800
categories: MySQL 
tag: ['MySQL']
---

* content
{:toc}

---
 * `binlog`格式
 
 ```sql
 show variables like 'binlog_format';
 ```
 
 ```
 +---------------+-------+
 | Variable_name | Value |
 +---------------+-------+
 | binlog_format | ROW   |
 +---------------+-------+
 1 row in set (0.00 sec)
 ```
 
 * 修改binlog_format
 
 ```sql
 set globle binlog_format='MIXED'
 
 vi /etc/my.cnf
 ```
 
 * 开启binlog
 
 ```sql
 set sql_log_bin=1
 ```
 
 
 * binlog 是否打开
 
 ```sql
 show variables like 'log_bin';
 ```
 
 ```
 +---------------+-------+
 | Variable_name | Value |
 +---------------+-------+
 | log_bin       | ON    |
 +---------------+-------+
 1 row in set (0.11 sec)
 ```
 
 * 获取binlog文件列表
 
 ```sql
 show binary logs;
 ```
 
 ```
 +------------------+-----------+
 | Log_name         | File_size |
 +------------------+-----------+
 | mysql-bin.000001 |      2267 |
 | mysql-bin.000002 |     21550 |
 | mysql-bin.000003 |      9581 |
 | mysql-bin.000004 |     15458 |
 | mysql-bin.000005 |       154 |
 | mysql-bin.000006 |       154 |
 | mysql-bin.000007 |      2051 |
 | mysql-bin.000008 |       154 |
 +------------------+-----------+
 8 rows in set (0.00 sec)
 ```
 
 
 * 查看当前正在写入的binlog文件
 
 ```sql
 show master status;
 ```
 ```
 +------------------+----------+--------------+------------------+-------------------+
 | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
 +------------------+----------+--------------+------------------+-------------------+
 | mysql-bin.000008 |      154 |              |                  |                   |
 +------------------+----------+--------------+------------------+-------------------+
 1 row in set (0.00 sec)
 ```
 
 * 查看指定binlog文件的内容
 
 
 ```
 show binlog events in 'mysql-bin.000007'
 ```
 
 ```
 +------------------+-----+-------------+-----------+-------------+---------------------------------------+
 | Log_name         | Pos | Event_type  | Server_id | End_log_pos | Info                                  |
 +------------------+-----+-------------+-----------+-------------+---------------------------------------+
 | mysql-bin.000007 |   4 | Format_desc |         1 |         123 | Server ver: 5.7.22-log, Binlog ver: 4 |
 +------------------+-----+-------------+-----------+-------------+---------------------------------------+
 1 row in set (0.00 sec)
 ```
 
 
 * `binlog`过期时间
 
 ```sql
 show variables like  'expire_logs_days';
 
 ```
 
 
 ```
 +------------------+-------+
 | Variable_name    | Value |
 +------------------+-------+
 | expire_logs_days | 0     |
 +------------------+-------+
 1 row in set (0.00 sec)
 ```
 
 * 其他`log`配置
 
 ```
 show variables like 'log_%';
 ```
 
 ```
 +----------------------------------------+--------------------------------------+
 | Variable_name                          | Value                                |
 +----------------------------------------+--------------------------------------+
 | log_bin                                | ON                                   |
 | log_bin_basename                       | /usr/local/var/mysql/mysql-bin       |
 | log_bin_index                          | /usr/local/var/mysql/mysql-bin.index |
 | log_bin_trust_function_creators        | OFF                                  |
 | log_bin_use_v1_row_events              | OFF                                  |
 | log_builtin_as_identified_by_password  | OFF                                  |
 | log_error                              | ./MacBook-Pro-2.local.err            |
 | log_error_verbosity                    | 3                                    |
 | log_output                             | FILE                                 |
 | log_queries_not_using_indexes          | OFF                                  |
 | log_slave_updates                      | OFF                                  |
 | log_slow_admin_statements              | OFF                                  |
 | log_slow_slave_statements              | OFF                                  |
 | log_statements_unsafe_for_binlog       | ON                                   |
 | log_syslog                             | OFF                                  |
 | log_syslog_facility                    | daemon                               |
 | log_syslog_include_pid                 | ON                                   |
 | log_syslog_tag                         |                                      |
 | log_throttle_queries_not_using_indexes | 0                                    |
 | log_timestamps                         | UTC                                  |
 | log_warnings                           | 2                                    |
 +----------------------------------------+--------------------------------------+
 ```