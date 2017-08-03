---
layout: post
title:  "Gson Date 解析异常"
date:   2016-07-07 13:31:01 +0800
categories: Java
tag: Gson
---

* content
{:toc}

---


## error log:

```
com.google.gson.JsonSyntaxException: 2017-08-03 20:04:21
        at com.google.gson.internal.bind.DateTypeAdapter.deserializeToDate(DateTypeAdapter.java:74)
        at com.google.gson.internal.bind.DateTypeAdapter.read(DateTypeAdapter.java:59)
        at com.google.gson.internal.bind.DateTypeAdapter.read(DateTypeAdapter.java:41)
        at com.google.gson.internal.bind.ReflectiveTypeAdapterFactory$1.read(ReflectiveTypeAdapterFactory.java:129)
        at com.google.gson.internal.bind.ReflectiveTypeAdapterFactory$Adapter.read(ReflectiveTypeAdapterFactory.java:220)
        at com.google.gson.Gson.fromJson(Gson.java:887)
        at com.google.gson.Gson.fromJson(Gson.java:852)
        at com.google.gson.Gson.fromJson(Gson.java:801)
        at com.google.gson.Gson.fromJson(Gson.java:773)
```
        

## code:


    ```
	Gson gson = new Gson();
    String dataAsJson = "{\"planTime\":\"2017-08-03 17:28:45\"}";
    RequirementPlanDTO requirementPlanDTO = gson.fromJson(dataAsJson, A.class);  
    ```    
        
## fix:


 ```
 Gson gson = new GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss").create();
 String dataAsJson = "{\"planTime\":\"2017-08-03 17:28:45\"}";
 RequirementPlanDTO requirementPlanDTO = gson.fromJson(dataAsJson, A.class);      
 ```