---
layout: post
title:  "MySql垂直输出查询结果"
date:   2017-04-08 12:29:00 +0800
categories: MySql
tag: 
---

* content
{:toc}

---

通过command，垂直输出查询结果：
>ego       (\G) Send command to mysql server, display result vertically.

普通输出：

```

MariaDB [test]> select * from dnc_email limit 1;
+----+--------------------------------------------------+-----------------------------------------------+--------+
| id | title                                            | content                                       | test   |
+----+--------------------------------------------------+-----------------------------------------------+--------+
| 10 | Re: CT To Automatically Register 400 000 Voters  | https://www.wikileaks.org/dnc-emails//get/1
 | 百货   |
+----+--------------------------------------------------+-----------------------------------------------+--------+

```

垂直输出：

```

MariaDB [test]> select * from dnc_email limit 1 \G
*************************** 1. row ***************************
     id: 10
  title: Re: CT To Automatically Register 400 000 Voters
content: https://www.wikileaks.org/dnc-emails//get/1

   test: 百货
1 row in set (0.00 sec)

```
其他command见：

```

MariaDB [test]> help

General information about MariaDB can be found at
http://mariadb.org

List of all MySQL commands:
Note that all text commands must be first on line and end with ';'
?         (\?) Synonym for `help'.
clear     (\c) Clear the current input statement.
connect   (\r) Reconnect to the server. Optional arguments are db and host.
delimiter (\d) Set statement delimiter.
edit      (\e) Edit command with $EDITOR.
ego       (\G) Send command to mysql server, display result vertically.
exit      (\q) Exit mysql. Same as quit.
go        (\g) Send command to mysql server.
help      (\h) Display this help.
nopager   (\n) Disable pager, print to stdout.
notee     (\t) Don't write into outfile.
pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
print     (\p) Print current command.
prompt    (\R) Change your mysql prompt.
quit      (\q) Quit mysql.
rehash    (\#) Rebuild completion hash.
source    (\.) Execute an SQL script file. Takes a file name as an argument.
status    (\s) Get status information from the server.
system    (\!) Execute a system shell command.
tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
use       (\u) Use another database. Takes database name as argument.
charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
warnings  (\W) Show warnings after every statement.
nowarning (\w) Don't show warnings after every statement.

For server side help, type 'help contents'

```