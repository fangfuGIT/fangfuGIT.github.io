---
layout: post
title:  "centos常用命令"
date:   2018-02-02 09:35:18
categories: centos
tags: centos
excerpt: centos的一些命令，再熟悉的命令，隔一段时间不用也会遗忘，故而记录一下
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

   
   
centos7下配置dns：  
nmcli connection show    # 查看当前启动的连接  
nmcli con mod eth0 ipv4.dns 61.139.2.69    #更改dns  
nmcli con up eth0    #配置生效   



