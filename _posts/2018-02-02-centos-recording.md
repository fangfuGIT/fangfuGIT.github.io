---
layout: post
title:  "centos6.X常用命令随手记"
date:   2018-02-02 09:35:18
categories: centos
tags: centos
excerpt: 再熟悉的命令，隔一段时间不用也会遗忘，故而记录一下
mathjax: true
---

```
cat /etc/rehat-release
cat /etc/issue
lsb_release -a
```

```
vi /etc/rc.d/rc.local  # 开机自动启动设置
```

```
vi /etc/selinux/config
    SELINUX=enforcing >>> SELINUX=disabled
```

```
setenforce 0
uptime
getconf LONG_BIT #查看64/32位
```




