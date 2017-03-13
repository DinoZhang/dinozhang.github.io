---
layout: post
title:  "git 常用命令"
date:   2016-06-06 12:29:00 +0800
categories: git
tag: git
---

* content
{:toc}

---


* `git branch`  查看本地分支，*表示当前分支

* `git clone repo.git` 把远程仓库下载下来

* `git remote -v` 查看仓库地址

* `git branch` 查看本地分支，*表示当前分支

* `git branch -a` 查看所有分支，包括远程的

* `git branch -d branch_name` 删除分支（-D 强制删除）

* `git checkout` 切换分支

* `git checkout -b new_branch_name` 本地创建新分支(`git branch new_branch_name`，`git checkout new_branch_name`)

* `git checkout file-name` 撤销未提交(`git checkout *.java`，`git checkout .`)

* `git push origin new_branch_name` 把新分支推送到远端

* `git push origin --delete branch_name` 删除远程分支

* `git pull` 拉取远端(`git fetch` ,`git merge`)

* `git pull origin branch_name` 拉取代码，会自动合并，有冲突会报错

* `git fetch` 拉取远端

* `git fetch` origin branch_name 拉取代码，不会自动合并

* `git add file_path` 加入将要提交的列表

* `git commit -m 'message' `提交代码

* `git push` 提交代码

* `git push --set-upstream origin branch_name` 提交代码，匹配远端的分支

* `git merge origin/branch_name` 合并远端代码

* `git stash` 暂存本地开发文件

* `git stash list` 查看暂存列表

* `git stash pop` 把暂存列表第一个恢复到本地

* `git cherry-pick commit_hash` 把某个版本合并到当前分支中

* `git rebase -i commit_hash` 从commit_hash到HEAD中选取需要的，这个会修改提交记录

* `git revert commit_hash` 回滚，会重新提交一个版本（反操作）

* `git revert -m 1 commit_hash`／`git revert -mainline 1 g` 回滚merge时需要指定回滚的分支

* `git reset --hard` 丢弃全部改动

* `git reset --hard commit_hash` 本地回到commit_hash

* `git show` 输出详细的增删情况

* `git config --global user.nam`  获取用户名

* `git config --global user.name "Your Name"` 配置用户名
 
* `git config --global user.email`  获取邮箱

* `git config --global user.email youremail@email.com` 配置邮箱

* `git log` 查看日志

* `git diff` 比较版本(`git diff --cached`，`git diff HEAD`，`git diff master1 master2`)