---
layout: post
title:  "MySQL 'on duplicate key update' 用法"
date:   2016-08-13 13:31:01 +0800
categories: 数据库
tag: MySQL
---

* content
{:toc}

---

>
If you specify ON DUPLICATE KEY UPDATE, and a row is inserted that would cause a duplicate value in a UNIQUE index or PRIMARY KEY, MySQL performs an UPDATE of the old row. For example, if column a is declared as UNIQUE and contains the value 1, the following two statements have similar effect:
>
```sql
INSERT INTO table (a,b,c) VALUES (1,2,3) 
   ON DUPLICATE KEY UPDATE c=c+1;
>
UPDATE table SET c=c+1 WHERE a=1;
```

如果在`INSERT`中指定`ON DUPLICATE KEY UPDATE`,并且插入将在`UNIQUE`索引或`PRIMARY KEY`中导致重复值的行,`MySQL`执行旧行的更新。比如，如果列a被声明为`UNIQUE`并且包含值1,以下两句话有相同的效果。