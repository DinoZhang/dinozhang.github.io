---
layout: post
title:  "Linux 常用命令"
date:   2017-03-31 12:29:00 +0800
categories: Linux
tag: ['Linux']
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
>
cat /etc/issue  (linux版本信息)
>
cat /proc/version （linux版本信息）

* `tac` 反向显示文件

> tac a.txt

* `nl` 显示行号

> nl a.txt

* `scp` 远程复制

>  scp user@server:/home/admin/2017-03-31.log .

* `ls` 列出文件

>
ls -al （所有文件及详细信息）
>
ls -lh  （详细信息文件大小`–human-readable`）

* `chmod` 权限 (d:目录 r:读4 w:写2 x:执行2)

>chmod 751 filename

* `tar` 压缩（c:create x:extract v:verbosely f:file）

>
tar cvf FileName.tar DirName (压缩)
>
>tar xvf FileName.tar （ 解压）

* `chown` 文件拥有者

> chown

* `wc` wordcount(line word byte)

>  wc a.txt
>
>2 2 4 a.txt

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
ps -ef \|less
>
history \| less

* `tail` 查看文件

>
>tail -n 5 2017-03-31.log (显示后5行)
>
>tail -f 2017-03-31.log (循环查看)
>>The -f option causes tail to not stop when end of file is
             reached, but rather to wait for additional data to be appended to
             the input.  The -f option is ignored if the standard input is a
             pipe, but not if it is a FIFO.

* `grep` 查看文件（过滤）

>
grep 'log'  log.log
>
grep -c 'log'  log.log (行的数量)

* `sed` 查看文件（编辑）

>
sed  -n '/6000060171931546/p' 2016-12-08.log (查找输出，不显示原内容)
>
grep -c 'log'  log.log (行的数量)

### 查找文件

* `which` 查看可执行文件的位置

>
> which which
>
> /usr/bin/which
>

* `whereis` 查看文件的位置 (从数据库中查找数据)

>
>whereis java
>
>/usr/bin/java
>

* `locate` 配合数据库查看文件位置(从数据库中查找数据)

>locate /etc/sh
>
>/etc/shadow
>
>/etc/shadow-
>
>/etc/shadowsocks.json
>
>/etc/shells
>


* `find`   实际搜寻硬盘查询文件名称

>find -amin 2 （两分钟前访问文件）
>
>./.ssh
>
>
>find . -name "*.log" (当前目录查找log)
>
>./.docker/machine/machines/default/default/Logs/VBox.log
>
>find ~/ -perm 777  （根据权限查找）
>
>/home/leinuo/data.sh
>
>find . -type f -name "*.sh" (根据文件类型查找)
>
>./data.sh
>
>find . -type d \| sort (查找当前所有目录并排序)
>
>.
>
>./.git
>find . -type f -exec ls -lh {} \; (find exec)
>
>-rw-r--r-- 1 leinuo leinuo 124 10月 16 2014 ./.bashrc
>
>-rwxrwxrwx 1 leinuo leinuo 881 12月  2 22:05 ./data.sh

## 磁盘
* `df` 空间使用

>
>df
>
>Filesystem     1K-blocks    Used Available Use% Mounted on
>
>/dev/xvda1     103079200 4454712  93381716   5% /
>
>tmpfs             960324       0    960324   0% /dev/shm
>
>df -lh (h:1024 H:1000)
>
>Filesystem      Size  Used Avail Use% Mounted on
>
>/dev/xvda1       99G  4.3G   90G   5% /
>
>tmpfs           938M     0  938M   0% /dev/shm
>

* `du` 文件空间使用

>du -h
>
>20K	./.ssh
>
>92K	.

## 进程

*  `top`

>  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
>
> 6242 root      20   0  310m 8692 2712 S  0.7  0.5 554:55.54 AliHids
 watchdog/0


* `ps` 进程

> ps
>
>  PID TTY          TIME CMD
>
>14631 pts/3    00:00:00 bash
>
>14650 pts/3    00:00:00 ps

>ps -ef\|grep ssh (与grep同时使用)
>
>root      4371     1  0  2016 ?        00:00:04 /usr/sbin/sshd

>ps -eo pid,lstart,etime \| grep 18557
>
>56627 日  5/28 22:48:31 2017      01-17:20:38

*  `kill`

>  kill -9 14815

* `free`

>free
>             total       used       free     shared    buffers     cached
>
>Mem:       1920648     671012    1249636          0     250992     190412
>
>-/+ buffers/cache:     229608    1691040
>
>Swap:            0          0          0
>