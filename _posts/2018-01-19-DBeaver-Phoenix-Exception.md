---
layout: post
title:  "DBeaver连接Phoenix报错"
date:   2018-01-19 12:29:00 +0800
categories: 数据库
tag:  ['DBeaver', 'Phoenix']
---

* content
{:toc}
---

`DBeaver`是一个免费多平台数据库工具。支持任何具有`JDBC`驱动程序的数据库（基本上意味着任何数据库。使用`DBeaver`连接`Phoenix`会报错。

## 报错


>
ERROR:
Unexpected driver error occurred while connecting to database
java.lang.RuntimeException: class org.apache.hadoop.security.JniBasedUnixGroupsMappingWithFallback not org.apache.hadoop.security.GroupMappingServiceProvider




## 解决


 `DBeaver`第一次创建数据源会报错，重启 `DBeaver`才会生效。
 


## 参考

[https://github.com/dbeaver/dbeaver/issues/1863](https://github.com/dbeaver/dbeaver/issues/1863)

 