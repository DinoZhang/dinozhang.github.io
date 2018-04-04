---
layout: post
title:  "Linux JSON 解析"
date:   2017-12-21 12:29:00 +0800
categories: Linux
tag: 
---

* content
{:toc}

---
`jq`是一个轻量级且灵活的命令行JSON处理器。


>
https://github.com/stedolan/jq


# 安装

CentOS

```
sudo yum install jq -y
```

Mac OS X

```
brew install jq
```


# 使用

* 获取数据（以天气为例）

```
curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E
```

```
{"date":"20180404","message":"Success !","status":200,"city":"杭州","count":776,"data":{"shidu":"88%","pm25":40.0,"pm10":77.0,"quality":"良","wendu":"20","ganmao":"极少数敏感人群应减少户外活动","yesterday":{"date":"03日星期二","sunrise":"05:48","high":"高温 27.0℃","low":"低温 17.0℃","sunset":"18:19","aqi":53.0,"fx":"南风","fl":"<3级","type":"阵雨","notice":"阵雨来袭，出门记得带伞"},"forecast":[{"date":"04日星期三","sunrise":"05:47","high":"高温 24.0℃","low":"低温 15.0℃","sunset":"18:20","aqi":72.0,"fx":"东北风","fl":"3-4级","type":"阵雨","notice":"阵雨来袭，出门记得带伞"},{"date":"05日星期四","sunrise":"05:46","high":"高温 20.0℃","low":"低温 10.0℃","sunset":"18:20","aqi":78.0,"fx":"北风","fl":"4-5级","type":"中雨","notice":"记得随身携带雨伞哦"},{"date":"06日星期五","sunrise":"05:45","high":"高温 18.0℃","low":"低温 9.0℃","sunset":"18:21","aqi":61.0,"fx":"西北风","fl":"4-5级","type":"多云","notice":"阴晴之间，谨防紫外线侵扰"},{"date":"07日星期六","sunrise":"05:44","high":"高温 16.0℃","low":"低温 7.0℃","sunset":"18:21","aqi":58.0,"fx":"西北风","fl":"4-5级","type":"晴","notice":"愿你拥有比阳光明媚的心情"},{"date":"08日星期日","sunrise":"05:43","high":"高温 21.0℃","low":"低温 11.0℃","sunset":"18:22","aqi":52.0,"fx":"西南风","fl":"<3级","type":"晴","notice":"愿你拥有比阳光明媚的心情"}]}}
```

* 使用`jq`格式化

```
curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq
```



```
{
  "date": "20180404",
  "message": "Success !",
  "status": 200,
  "city": "杭州",
  "count": 773,
  "data": {
    "shidu": "88%",
    "pm25": 40,
    "pm10": 77,
    "quality": "良",
    "wendu": "20",
    "ganmao": "极少数敏感人群应减少户外活动",
    "yesterday": {
      "date": "03日星期二",
      "sunrise": "05:48",
      "high": "高温 27.0℃",
      "low": "低温 17.0℃",
      "sunset": "18:19",
      "aqi": 53,
      "fx": "南风",
      "fl": "<3级",
      "type": "阵雨",
      "notice": "阵雨来袭，出门记得带伞"
    },
    "forecast": [
      {
        "date": "04日星期三",
        "sunrise": "05:47",
        "high": "高温 24.0℃",
        "low": "低温 15.0℃",
        "sunset": "18:20",
        "aqi": 72,
        "fx": "东北风",
        "fl": "3-4级",
        "type": "阵雨",
        "notice": "阵雨来袭，出门记得带伞"
      },
      {
        "date": "05日星期四",
        "sunrise": "05:46",
        "high": "高温 20.0℃",
        "low": "低温 10.0℃",
        "sunset": "18:20",
        "aqi": 78,
        "fx": "北风",
        "fl": "4-5级",
        "type": "中雨",
        "notice": "记得随身携带雨伞哦"
      },
      {
        "date": "06日星期五",
        "sunrise": "05:45",
        "high": "高温 18.0℃",
        "low": "低温 9.0℃",
        "sunset": "18:21",
        "aqi": 61,
        "fx": "西北风",
        "fl": "4-5级",
        "type": "多云",
        "notice": "阴晴之间，谨防紫外线侵扰"
      },
      {
        "date": "07日星期六",
        "sunrise": "05:44",
        "high": "高温 16.0℃",
        "low": "低温 7.0℃",
        "sunset": "18:21",
        "aqi": 58,
        "fx": "西北风",
        "fl": "4-5级",
        "type": "晴",
        "notice": "愿你拥有比阳光明媚的心情"
      },
      {
        "date": "08日星期日",
        "sunrise": "05:43",
        "high": "高温 21.0℃",
        "low": "低温 11.0℃",
        "sunset": "18:22",
        "aqi": 52,
        "fx": "西南风",
        "fl": "<3级",
        "type": "晴",
        "notice": "愿你拥有比阳光明媚的心情"
      }
    ]
  }
}
```

* 获取元素

```
curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq .data.yesterday
```


```
{
  "date": "03日星期二",
  "sunrise": "05:48",
  "high": "高温 27.0℃",
  "low": "低温 17.0℃",
  "sunset": "18:19",
  "aqi": 53,
  "fx": "南风",
  "fl": "<3级",
  "type": "阵雨",
  "notice": "阵雨来袭，出门记得带伞"
}
```


* 获取数组元素

```
curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq .data.forecast[0]
```

```
{
  "date": "04日星期三",
  "sunrise": "05:47",
  "high": "高温 24.0℃",
  "low": "低温 15.0℃",
  "sunset": "18:20",
  "aqi": 72,
  "fx": "东北风",
  "fl": "3-4级",
  "type": "阵雨",
  "notice": "阵雨来袭，出门记得带伞"
}

```

* 获取多个数据元素

```
curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq .data.forecast[0,1]
```

```
{
  "date": "04日星期三",
  "sunrise": "05:47",
  "high": "高温 24.0℃",
  "low": "低温 15.0℃",
  "sunset": "18:20",
  "aqi": 72,
  "fx": "东北风",
  "fl": "3-4级",
  "type": "阵雨",
  "notice": "阵雨来袭，出门记得带伞"
}
{
  "date": "05日星期四",
  "sunrise": "05:46",
  "high": "高温 20.0℃",
  "low": "低温 10.0℃",
  "sunset": "18:20",
  "aqi": 78,
  "fx": "北风",
  "fl": "4-5级",
  "type": "中雨",
  "notice": "记得随身携带雨伞哦"
}
```

* 获取所有元素

```
curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq .data.forecast[]
```

```
{
  "date": "04日星期三",
  "sunrise": "05:47",
  "high": "高温 24.0℃",
  "low": "低温 15.0℃",
  "sunset": "18:20",
  "aqi": 72,
  "fx": "东北风",
  "fl": "3-4级",
  "type": "阵雨",
  "notice": "阵雨来袭，出门记得带伞"
}
{
  "date": "05日星期四",
  "sunrise": "05:46",
  "high": "高温 20.0℃",
  "low": "低温 10.0℃",
  "sunset": "18:20",
  "aqi": 78,
  "fx": "北风",
  "fl": "4-5级",
  "type": "中雨",
  "notice": "记得随身携带雨伞哦"
}
{
  "date": "06日星期五",
  "sunrise": "05:45",
  "high": "高温 18.0℃",
  "low": "低温 9.0℃",
  "sunset": "18:21",
  "aqi": 61,
  "fx": "西北风",
  "fl": "4-5级",
  "type": "多云",
  "notice": "阴晴之间，谨防紫外线侵扰"
}
{
  "date": "07日星期六",
  "sunrise": "05:44",
  "high": "高温 16.0℃",
  "low": "低温 7.0℃",
  "sunset": "18:21",
  "aqi": 58,
  "fx": "西北风",
  "fl": "4-5级",
  "type": "晴",
  "notice": "愿你拥有比阳光明媚的心情"
}
{
  "date": "08日星期日",
  "sunrise": "05:43",
  "high": "高温 21.0℃",
  "low": "低温 11.0℃",
  "sunset": "18:22",
  "aqi": 52,
  "fx": "西南风",
  "fl": "<3级",
  "type": "晴",
  "notice": "愿你拥有比阳光明媚的心情"
}
```

# 其他用法

 `jq`有很多内置函数并且支持管道
 
 
 * 加减运算
 

```
 echo "{\"a\": 7}"|jq '.a + 1'
```
  
  ```
  8
  ```
 
 
 * 选择器

```
 echo '[1,2,3]' |jq ' map(select(. >= 2))'
```
 
 
 ```
 [
  2,
  3
]
 ```
 
 * 向下取整

```
echo '999.99'|jq 'floor'
```
 
 ```
 999
 ```
 
* 开方

```
echo '999.99'|jq 'sqrt'
```

```
31.6226184874055
```
 
 * 统计元素数量
 
 
```
 curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq '.data.forecast[] | length'
```
 
 
 ```
10
10
10
10
10
 ```
  * 统计数据长度
 
```
 curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq '.data.forecast | length'
```
 
 ```
 5
 ```
 
 * 类型转换

```
 curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq '.data.yesterday.aqi| tostring'
```
 
 ```
 "53"
 ```

* 数组排序

```
curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq '.data.forecast'|jq 'sort_by(.aqi)'|jq .[].aqi
```

```
52
58
61
72
78
```

* 数组反转

```
curl https://www.sojson.com/open/api/weather/json.shtml?city=%E6%9D%AD%E5%B7%9E |jq '.data.forecast'|jq 'sort_by(.aqi) |reverse'|jq .[].aqi
```

```
78
72
61
58
52
```