---
layout: post
title:  "keepalived记录及原理"
date:   2018-04-02 18:18:00
categories: keepalived
tags: ssh
excerpt: keepalived的实现原理
mathjax: true
---

常听到LVS+keepalived的负载均衡格式，实际上LVS和keepalived是两回事。
我们可以用LVS+keepalived，也可以用nginx+keepalived
keepalived功能在于负载，nginx在于分流

Keepalived具有3-5层的交换功能。
3层机理是发送ICMP数据包给某个服务器，如果不通，则认为其故障，并从服务器群中剔除。
4层机理是检测TCP端口号状态来判断某台服务器是否故障，如果故障，则从服务器群中剔除。
5层机理是根据用户的设定检查某个服务器应用程序是否正常运行，如果不正常，则从服务器群中剔除。
当然这个是keepalive的内部负载原理。

keepalived的工作原理是VRRP（Virtual Router Redundancy Protocol）虚拟路由冗余协议。
keepalived的主节点A会通过发送arp广播包：申明VIP的IP地址绑定节点A的网卡MAC
当节点A出现故障，节点B会自动检测到，并重新发布arp广播包：申明VIP的IP地址绑定节点B的网卡MAC


