---
layout: post
title:  "Hbase Shell Exception"
date:   2017-12-05 12:29:00 +0800
categories: Hbase
tag: Hbase
---

* content
{:toc}

---

# Hbase Shell Exception

## 日志

>
2017-12-05 17:41:47,911 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
NativeException: java.io.IOException: java.lang.reflect.InvocationTargetException
  initialize at /Users/dinozhang/git/alihbase-1.1.1/bin/../lib/ruby/hbase/hbase.rb:42
      (root) at /Users/dinozhang/git/alihbase-1.1.1/bin/../bin/hirb.rb:118


## 原因

`hbase-site.xml`文件中异常换行或空格

```xml
<configuration>
    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>
        zk
        </value>
    </property>
</configuration>
```

fix:

```xml
<configuration>
    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zk</value>
    </property>
</configuration>
```