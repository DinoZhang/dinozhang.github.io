---
layout: post
title:  "Maven mirror"
date:   2016-07-07 13:31:01 +0800
categories: Java
tag: ['Maven', 'Java']
---

* content
{:toc}

---

修改conf目录下settings.xml

```xml
  
  <mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
  
```
