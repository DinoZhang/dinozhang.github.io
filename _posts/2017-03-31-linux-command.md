---
layout: post
title:  "Linux 常用命令"
date:   2017-03-31 12:29:00 +0800
categories: Linux

---

* content
{:toc}

---
## 目录文件

* `cd`变换目录
> cd ~

* `pwd`显示目前的目录

* `mkdir`创建一个新的目录
> mkdir test

* `rmdir`删除一个空的目录
> rmdir test

* `rm` 删除(**慎用 rm -rf**)
>
rm -f test （force 强制）
>
rm -r test (recursive 递归)

* `mv` 移动（删除）文件
> mv a.txt b.txit

* `cp` 复制
> cp a.txt /admin/test

* `touch` 新建文件
> touch a.txt

* `vim` vim编辑文件
> vim a.txit

* `cat` 连接显示文件
>
cat a.txt （显示文件）
>
cat a.txt b.txt > c.txt (合并文件)
>
cat a.txt >> c.txt (追加文件)

* `tac` 反向显示文件
> cat a.txt