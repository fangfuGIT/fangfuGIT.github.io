---
layout: post
title:  "centos免密钥登录的记录"
date:   2018-02-02 16:18:00
categories: centos
tags: ssh
excerpt: 关于centos免密钥登录的一些笔记，一般用于计划任务调用脚本执行远程操作的。
mathjax: true
---

实现服务器与服务器之间免密码登录，用于计划任务调用shell脚本执行远程操作。

比如A服传文件到B服.

A服上执行：
```
ssh-keygen -t rsa
```

回车回车回车

```
scp -P 22 /root/.ssh/id_rsa.pub root@B服IP:/root/.ssh/authorized_keys
```

B服上执行：

```
chmod 600 authorized_keys
```

在A服上执行脚本测试

当然，有时候按需求B服上也需要执行一下ssh-keygen -t rsa

