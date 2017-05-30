---
layout: post
title:  "Redis 排序分页"
date:   2017-05-30 13:31:01 +0800
categories: Redis
tag:  ['Redis', 'Java']
---

* content
{:toc}

---

## 原理

>SORT key [BY pattern] [LIMIT offset count] [GET pattern [GET pattern ...]] [ASC | DESC] [ALPHA] [STORE destination]
>
>返回或保存给定列表、集合、有序集合 key 中经过排序的元素。

## Redis 命令


对于如下结构：

| id| nicke| updateTime |
|---|---|---|
|111|jack| 1493709972068 |
|2111|tom| 1495437972186 |
|2122|marry| 1494573972186 |

Redis存储：

执行：

```
127.0.0.1:6379> sadd testkey  111 2111 2122
(integer) 3
```
查看：

```
127.0.0.1:6379> smembers testkey
1) "111"
2) "2111"
3) "2122"
```

执行：

```
127.0.0.1:6379> hset testkey1 data {\"id\":2111,\"nick\":\"tom\",\"updateTime\":1495437972186} updateTime 20170522152612 id 2111
```

查看：

```
127.0.0.1:6379> hgetall testkey1
1) "data"
2) "{\"id\":2111,\"nick\":\"tom\",\"updateTime\":1495437972186}"
3) "updateTime"
4) "20170522152612"
5) "id"
6) "2111"
```

……

查看：

```
127.0.0.1:6379> keys testkey*
1) "testkey2"
2) "testkey0"
3) "testkey1"
4) "testkey"
```

按照id降序：

```
127.0.0.1:6379> SORT testkey by testkey*->id  get testkey*->data desc limit 0 3
1) "{\"id\":2122,\"nick\":\"marry\",\"updateTime\":1494573972186}"
2) "{\"id\":2111,\"nick\":\"tom\",\"updateTime\":1495437972186}"
3) "{\"id\":111,\"nick\":\"jack\",\"updateTime\":1493709972068}"
```




## Java 实现


生成排序列表：

```

	/**
	 * generate sort list
	 * @param ns
	 * @param key
	 * @param list
	 * @param sortFileds
     * @param <T>
	 * @return
	 */
	public <T> List<T> generateSortList(String ns, String key, List<T> list, String ... sortFileds) {
		for (int i = 0; i < list.size(); i++) {
			try {
                Map<String, String> hmMap = new HashMap<>();
                T t = list.get(i);
			    for(String sortFiled:sortFileds){
                    Field field = t.getClass().getDeclaredField(sortFiled);
                    field.setAccessible(true);
                    Object value = field.get(t);

                    if(value instanceof Date){
                        SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMddHHmmss");
                        hmMap.put(field.getName(), sdf.format(value));
                    }else {
                        hmMap.put(field.getName(), value.toString());
                    }
                }
				// 源数据
				hmMap.put("data", JSON.toJSONString(t));

				redisTemplate.opsForHash().putAll(generateKey(ns, key) + i, hmMap);
				redisTemplate.opsForSet().add(generateKey(ns, key), i + "");

			} catch (NoSuchFieldException e) {
				e.printStackTrace();
			} catch (IllegalAccessException e) {
				e.printStackTrace();
			}
		}

		return null;

	}
```

排序接口：
```
  /**
     * redis list sort
     * @param ns
     * @param key
     * @param cla
     * @param orderType
     * @param sortfield
     * @param offset
     * @param count
     * @param <T>
     * @return
     */
    @SuppressWarnings("unchecked")
    public <T> List<T> sortList(String ns, String key, Class<T> cla,String orderType,String sortfield, int offset, int count) {

        SortParameters.Order order = SortParameters.Order.valueOf(orderType);
        SortParameters.Range range = new SortParameters.Range(offset, count);

        SortQuery<String> query = SortQueryBuilder.sort(generateKey(ns, key))
                .by(generateKey(ns, key) + "*->" + sortfield)
                .get(generateKey(ns, key) + "*->data")
                .order(order)
                .limit(range)
                .build();

        List<Object> results = redisTemplate.sort(query);
        List<T> resultList = new ArrayList<>();
        for (Object result : results) {
            resultList.add(JSON.parseObject((String) result, cla));
        }

        return resultList;
	}
```

测试：
```
/**
 * Created by dinozhang on 2017/5/8.
 */
public class RedisTest {


	private static ApplicationContext applicationContext = new ClassPathXmlApplicationContext("redis.xml");
	private static RedisTemplate<String, Object> redisTemplate = (RedisTemplate<String, Object>) applicationContext.getBean("redisTemplate");

	public static void main(String[] args) {
		UserDO userDo = new UserDO();
		userDo.setId(111l);
		userDo.setUpdateTime(new DateTime().withDayOfMonth(2).toDate());
		userDo.setNick("jack");

		UserDO userDo1 = new UserDO();
		userDo1.setId(2111l);
		userDo1.setNick("tom");
		userDo1.setUpdateTime(new DateTime().withDayOfMonth(22).toDate());

		UserDO userDo2 = new UserDO();
		userDo2.setId(2122l);
		userDo2.setNick("marry");
		userDo2.setUpdateTime(new DateTime().withDayOfMonth(12).toDate());

		List<UserDO> userDOs = new ArrayList<>();
		userDOs.add(userDo);
		userDOs.add(userDo1);
		userDOs.add(userDo2);
		
		String[] sortFields = { "id", "updateTime" };

		generateSortList("testkey", userDOs, sortFields);
		List<UserDO> userDOs1 = sortList("testkey", UserDO.class, "DESC", "id", 0, 5);
		List<UserDO> userDOs2 = sortList("testkey", UserDO.class, "DESC", "updateTime", 0, 5);
		System.out.println(userDOs1.size());

	}
}

```
Redis配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 连接池配置 最大空闲数、最大连接数、最长等待时间、连接是否可用 -->
    <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <property name="maxIdle" value="10" />
        <property name="maxTotal" value="20" />
        <property name="maxWaitMillis" value="300" />
        <property name="testOnBorrow" value="true" />
    </bean>

    <!-- 连接配置 地址、端口 -->
    <bean id="connectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory" >
        <property name="hostName" value="127.0.0.1" />
        <property name="port" value="6379" />
        <property name="password" value="" />
        <property name="poolConfig" ref="poolConfig" />
    </bean>

    <!-- 暴露一个redisTemplate 用作redis一系列操作 -->
    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">
        <property name="connectionFactory" ref="connectionFactory" />
        <!--指定序列化方式避免key值出现\xac\xed\x00\x05t\x00-->
        <property name="KeySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"></bean>
        </property>
        <property name="ValueSerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"></bean>
        </property>
        <property name="HashKeySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"></bean>
        </property>
        <property name="HashValueSerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"></bean>
        </property>
    </bean>
</beans>
```