---
layout: post
title:  "Linux Shell监控进程"
date:   2018-03-01 12:29:00 +0800
categories: Linux 
tag: ['Linux']
---

* content
{:toc}

---


防止进程意外关闭，定时查询进程存在进行预警或重启。

```
#!/bin/bash
num=$(ps -ef|grep canal-1.0 -c)
if [[ ${num} -lt 2 ]];then
    echo  $(date)"失败"
    nohup java -jar  ~/canal-1.0.jar >/dev/null  2>&1 &
    echo  $(date)"重启成功"
fi
```




添加定时任务`crontab -e`



```
*/1 * * * *  /home/admin/canal_monitor.sh 2>&1 >> ～/canal_monitor.log
```

查看监控执行日志


```
sudo less /var/log/cron
```