---
layout: post
title:  "Phoenix 介绍及使用"
date:   2017-12-22 12:29:00 +0800
categories: 数据库
tag: ['Hbase', 'Phoenix']
---

* content
{:toc}

---

## 介绍



>
Apache Phoenix enables OLTP and operational analytics in Hadoop for low latency applications by combining the best of both worlds:
>
* the power of standard SQL and JDBC APIs with full ACID transaction capabilities and
* the flexibility of late-bound, schema-on-read capabilities from the NoSQL world by leveraging HBase as its backing store

>
Apache Phoenix is fully integrated with other Hadoop products such as Spark, Hive, Pig, Flume, and Map Reduce.

## shell

* 进入

```sql
./sqlline.py zk1,zk2,zk3
```
* 退出

```sql
!quit
```

* 列表

```sql
!tables
```

* 表详情

```sql
!describe table_name
```

*  垂直输出

```sql
!outputformat vertical
```

>
```
ROWKEY           00001f6750f9
PT               20171221
c1               3711075160
c2               767742
c3               2
c4               136
c5               2
c5               2
c7               3
```

## 基本语法

* 创建表

```sql

CREATE TABLE IF NOT EXISTS  table_name 
(ROWKEY VARCHAR, PT VARCHAR,c1 UNSIGNED_LONG, 
c2  UNSIGNED_LONG,c3 UNSIGNED_LONG,c4 UNSIGNED_LONG,c5 UNSIGNED_LONG,
 CONSTRAINT my_pk PRIMARY KEY (ROWKEY))COLUMN_ENCODED_BYTES=NONE;
```

* 添加字段

```sql
ALTER TABLE table_name ADD c7 UNSIGNED_LONG,c8 UNSIGNED_LONG
```


* 删除表

```sql
drop table table_name;
```

* 创建索引


```sql
create index column_index on test (c1);

```
这样只会在查询`c1`是生效，即`select c1 from table_name where c1 = '1'`时才生效，对于`select * from table_name where c1 = '1'`则必须添加覆盖索引：



```sql
create index cdd_pt_sort_index on table_name (c1,c2 desc) INCLUDE(c1,c2,c3,c4
 );

```

* 删除索引


```sql
drop index cdd_pt_sort_index on table_name
```

## 其他

* 严格区分大小写

  默认大写，对于小写会转成大写，所以要使用小写需使用 `'`。
* 特殊数字编码

  Phoenix 与 Hbase 对数字的编码不同，对于Phoenix里面的存储跟我们想象的不一样，它是首位取反的。 举个例子，有一个tinyint的56，正常的二进制表示为00111000，然而在这里表示为10111000，这样，就保证了在Phoenix中，这些数作为rowkey的时候，负数在正数前面，以保证rowkey有序。
  
* 创建索引耗时
 >
  ```
17/12/22 17:47:07 INFO schema.MetaDataClient: Created index TEST_INDEX at 1513936027485
  458,328 rows affected (346.306 seconds)
```