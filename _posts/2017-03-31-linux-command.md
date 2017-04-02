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

* `nl` 显示行号
> nl a.txt

* `scp` 远程复制
>  scp user@server:/home/admin/2017-03-31.log .

* `ls` 列出文件
>
ls -al （所有文件及详细信息）
>
ls -lh  （详细信息文件大小`–human-readable`）

### 查看文件内容

* `more` 查看文件 (`space`:下一页 b:上一页）
>
more 2017-03-31.log
>
`space`:下一页
>
b:上一页

* `less` 查看文件
>  less 2017-03-31.log
>
G （行尾）
>
g （行首）
>
／（向下搜索）
>
？（向上搜索）
>
n (重复前一个搜索)
>
N (反向重复前一个搜索)
>
`pagedown` (向下翻动一页)
>
`[pageup]` (向上翻动一页)
>
q (退出)
>
ps -ef |less
>
history | less

* `tail` 查看文件
>
>tail -n 5 2017-03-31.log (显示后5行)
>
>tail -f 2017-03-31.log (循环查看)
>>The -f option causes tail to not stop when end of file is
             reached, but rather to wait for additional data to be appended to
             the input.  The -f option is ignored if the standard input is a
             pipe, but not if it is a FIFO.