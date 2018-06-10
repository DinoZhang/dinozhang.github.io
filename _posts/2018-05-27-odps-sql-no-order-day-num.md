---
layout: post
title:  "ODPS SQL 连续无订单天数统计"
date:   2018-05-27 12:29:00 +0800
categories: 大数据 
tag: ['ODPS']
---

* content
{:toc}

---

## 问题

对于一张门店统计表`day_shop_orders`,计算`20180503`日无订单门店的连续无订单天数。



* 原始表

| shop_id| order_num | pt(时间)|……|
|---|----|----|----|
|1|0|20180501||
|1|0| 20180502|
|1|0|20180503|
|888|0| 20180503|
|999|1440|20180501||
|999|11| 20180502|
|999|333|20180503|
|1111|0|20180502|


* 结果表

| shop_id| day_num |
|---|----|----|
|1|3|
|888|1| 



# 方案




1.**ROW_NUMBER**计算行号

>
ROW_NUMBER() OVER( [ PRITITION BY col1] ORDER BY col2[ DESC ] ) 



```sql
select pt,shop_id,row_number() over(partition by shop_id order by pt desc) as row  
from day_shop_orders
where order_num = 0)
```

结果：

| shop_id| row | pt(时间)|
|---|----|----|
|1|1|20180503||
|1|2| 20180502|
|1|3|20180501|
|888|1| 20180503|
|1111|0|20180502|


2.使用**pt+row**和**shop_id**分组


```sql

select shop_id,day_num from (
select shop_id,cast(to_char(dateadd(to_date(pt,'yyyymmdd'), row, 'dd'),'yyyymmdd') as bigint) time ,
count(1) day_num from (
select pt,shop_id,row_number() over(partition by shop_id order by pt desc) as row  
from day_shop_orders
where order_num = 0  and pt <= ${bdp.system.bizdate})
) temp  
group by shop_id,cast(to_char(dateadd(to_date(pt,'yyyymmdd'), row, 'dd'),'yyyymmdd') as bigint)
) where to_char(dateadd(to_date(time,'yyyymmdd'), -1, 'dd'),'yyyymmdd')  = ${bdp.system.bizdate}
```

结果：

| shop_id| day_num |
|---|----|
|1|3|
|888|1| 


# 其他

*  **postgresql** 及 **hive** 均支持 **ROW_NUMBER** 分区排序函数, **MySQL 8.0**开始支持 **ROW_NUMBER** 函数。
*  用户连续签到天数问题与上述类似。




