---
layout: post
title:  "Shell+WebHooks日志分析报警"
date:   2018-06-11 12:29:00 +0800
categories: Linux 
tag: ['Shell','WebHooks']
---

* content
{:toc}

---


## shell日志分析

统计每日来访`ip`

```bash
grep $time /var/log/shadowsocks.log -a|awk -F ':' '{print $7}'|sort|uniq -c|sort -k 1 -n -r|head -5
```

查看所在地

```bash
curl http://freeapi.ipip.net/$ip -s |sed -e 's/\"//g;s/\[//g;s/\]//g;'
```

## 接入钉钉WebHooks

```bash
   curl 'https://oapi.dingtalk.com/robot/send?access_token='$token'' \
  -H 'Content-Type: application/json' \
  -d '
  {"msgtype": "text",
  "text": {
  "content":"'$time@$(awk '{print $1}' ./tmp.log)@$(awk '{print $2}' ./tmp.log)@$loc'"
  }
  } ' -s
```

完整脚本：

```bash
#!/bin/bash

if [ ! -z $1 ];then
  token=$1
else
  token='digndintoken'
fi

time=$(date -d '-1 days' +"%Y-%m-%d")
#echo $time
log=$(grep $time /var/log/shadowsocks.log -a|awk -F ':' '{print $7}'|sort|uniq -c|sort -k 1 -n -r|head -5)

#指定分隔符
IFS=$'\n'
for line in $log
do
   echo $line > tmp.log
   ip=$(awk '{print $2}' ./tmp.log)
   #ip-->loc
   loc=$(curl http://freeapi.ipip.net/$ip -s |sed -e 's/\"//g;s/\[//g;s/\]//g;')
   echo $(awk '{print $1}' ./tmp.log) $(awk '{print $2}' ./tmp.log) $loc >> ss-analysis.log


   #result=$(cat ss-analysis.log|sed -e 's/\"//g;s/\[//g;s/\]//g;')

   curl 'https://oapi.dingtalk.com/robot/send?access_token='$token'' \
  -H 'Content-Type: application/json' \
  -d '
  {"msgtype": "text",
  "text": {
  "content":"'$time@$(awk '{print $1}' ./tmp.log)@$(awk '{print $2}' ./tmp.log)@$loc'"
  }
  } ' -s

  sleep 2s
done
```

## Linux定时任务

安装定时任务

```bash
crontab -e
```

```bash
0 16  * * * /var/log/ss-analysis.sh
```

