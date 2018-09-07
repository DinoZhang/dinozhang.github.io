---
layout: post
title:  "Jekyll-Mac-High-Sierra-Error"
date:   2018-09-06 12:29:00 +0800
categories: Jekyll
tag: Jekyll
---

* content
{:toc}

---

> `Mac` 升级到 `macOS High Sierra`后，`Jekyll`无法正常启动：
>

异常信息：

```shell
$jekyll -v
-bash: /usr/local/bin/jekyll: /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/bin/ruby: bad interpreter: No such file or directory
```

解决方案：


```shell
curl -sSL https://get.rvm.io | bash -s stable
```

```shell
source /Users/dinozhang/.rvm/scripts/rvm

```


```shell
rvm install 2.5.1
```

```shell
sudo gem install jekyll bundler
```