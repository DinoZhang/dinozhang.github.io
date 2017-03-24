---
layout: post
title:  "Mac OS和Linux下date命令异同比较"
date:   2017-03-14 12:29:00 +0800
categories: Linux
tag: ['Shell', 'Mac']
---

* content
{:toc}

## Mac OS

**Mac OS**使用**AT&T UNIX**版本**date**命令。

* `date` 当前时间
> 2017年 3月13日 星期一 23时48分07秒 CST

* `date +%c` 当前时间
> 一  3/13 23:49:57 2017

* `date -r 1489420972` 转换 **Unix timestamp**
> 2017年 3月14日 星期二 00时02分52秒 CST

* `date -r filename` 文件修改时间
> 2017年 3月13日 星期一 23时48分07秒 CST

* `date  "+%Y-%m-%d %H:%M:%S"` 格式化输入
> 2017-03-14	00:15:07

* `date -v-1M` 减一秒（其他yyyymmddHHMMSS）
> 2017年 3月13日 星期一 23时48分15秒 CST

* `date -v-1d "+%Y-%m-%d %H:%M:%S"` 减一天格式化shuchu
> 2017-03-13	00:15:07

* `date "+%N"` N
> N

## Linux

**Linux**使用**GNU**版本**date**命令。

* `date -s "2008-05-23 01:01:01"` 设置系统时间(root)

* `date` 当前时间
> 2017年 03月 13日 星期一 23:48:37 CST

* `date +%c` 当前时间
> 2017年03月13日 星期一 23时48分54秒

* `date +%Y-%m-%d%t%H:%M:%S%n%p` 格式化当前时间
&emsp;
> 2017-03-14	00:15:07
>>上午
>>
*  `date +%Y-%m-%d --date="+1 day"` 加一天
> 2017-03-15

*  `date +%Y-%m-%d --date="+1 day"` 加一月
> 2017-04-14

*  `date +%Y-%m-%d --date="+1 year` 加一年
> 2018-03-14

*  `date -d "+1 days" +%Y-%m-%d` 加一天
> 2018-03-15

*  `date -d "2 weeks" +%Y-%m-%d` 两周后
> 2018-03-28

*  `date -d "30 days ago` 30天前
> 2018-02-12

*  `date -d "jan 14 -2 weeks" +%Y-%m-%d` 两周前
> 2016-12-31

* `date "+%N"` nanoseconds(000000000-999999999)
> 532833721

## 用法相同

**Mac OS** 与 **Linux**在格式化输入用法相同

*  `date +%A` 星期
> 星期二

*  `date +%B` 月份
> 三月

*  `date +%b` 月份 同 `date +%h`
> 3

*  `date +%j` 一年中的第几天 (001-366)
> 073

*  `date +%m` 月份(第几个月)
> 03

*  `date +%U` 一年中的第几周(00-53,以周天为第一天)
> 11

*  `date +%W` 一年中的第几周(00-53,以周一为第一天)
> 11

*  `date +%y` 年份
> 17

*  `date +%Y` 年份
> 2017
