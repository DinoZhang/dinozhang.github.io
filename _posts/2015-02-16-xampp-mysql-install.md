---
layout: post
title:  "关于XAMPP环境MySQL服务无法启动的解决方法"
date:   2015-02-16 12:29:00 +0800
categories: 数据库
tag: ['MySQL', 'XAMPP']
---

* content
{:toc}

---

今天搭建**XAMP**开发环境时，**Apache**正常启动，但**MySql**无
法启动一直显示:
>
Executing “d:\xampp\mysql”

一直没有反应，翻阅日志文件发现**MySql**安装过程中出现:
> 
[mysql]Found Path: "C:\Program Files\MySQL\MySQL Server 5.5\bin\mysqld" –defaults-file="C:\Program Files\MySQL\MySQL Server 5.5\my.ini" MySQL            
> 
[mysql] Expected Path: d:\xampp\mysql\bin\mysqld.exe –defaults-file=d:\xampp\mysql\bin\my.ini mysql。

这是因为我的电脑已经安装了MySQL，默认启动的是以前的 mysql。

解决方法：

1.通过修改注册表的键值。

**[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\MySQL]**的 `ImagePath`从:

>
"C:\Program Files\MySQL\MySQL Server 5.5\bin\mysqld" 
–defaults-file="C:\Program Files\MySQL\MySQL Server 5.5\my.ini"

修改成新的xampp中位置:

>
"d:\xampp\mysql\bin\mysqld.exe" –defaults-file="d:\xampp\mysql\bin\my.ini"

然后重新`start`即可顺利启动**MySQL**。

2.卸载原来安装的**MySql**。不再赘述。