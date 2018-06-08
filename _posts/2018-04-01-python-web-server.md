---
layout: post
title:  "Python Web Server"
date:   2018-04-19 12:29:00 +0800
categories: Python 
tag: ['Python']
---

* content
{:toc}

---


使用`Python`快速搭建`Web`服务

```python
python -m SimpleHTTPServer 8000

```


`Python3`

```python
python3 -m http.server 8000
```


进入文件目录，后台持续运行

```bash
nohup python3 -m http.server 8000 >> ~/out.log 2>&1 &
```

进入 `http://ip:8000`,如果文件目录有`index.htm` 或 `index.html`则显示网页内容，
否则显示文件列表。