的---
layout: post
title:  "LVS+keepalived负载均衡DR模式TUN模式"
date:   2020-04-07 12:04:59
categories:  LVS
tags: LVS
excerpt: 
mathjax: true
---

LVS常用的三种模式：NAT、DR、TUN

NAT模式：
RealServer将自己的网关指向Director
可以运行任何支持TCP/IP的操作系统。但是扩展性有限，容易产生瓶颈。

LVS的DR模式的原理

LVS的TUN模式的原理


ipvsadm命令选项
```
-A        添加一个虚拟服务，使用ip地址、端口号、协议来唯一定义一个虚拟服务
-E        编辑一个虚拟服务
-D        删除一个虚拟服务
-C        清空虚拟服务列表
-R        从标准输入中还原虚拟服务列表
-S        保存虚拟服务规则至标准输出，输出规则可使用-R选项还原
-L        显示虚拟服务列表
-Z        虚拟服务器列表计数器清零（清空当前连接数）
-a        添加一台真实服务器
-e        编辑一台真实服务器
-d        减少一台真实服务器
-t        使用TCP服务，该参数后需加主机与端口信息
-u        使用UDP服务，该参数后需加主机与端口信息
-s        指定lvs的调度算法
-r        设置真实服务器IP与端口
-g        设置lvs工作模式为DR直连路由
-i        设置lvs工作模式为TUN隧道
-m        设置lvs工作模式为NAT地址转换模式
-w        指定真实服务器权重
-c        连接状态，配和-L使用
-n        数字格式显示
--stats   显示统计信息
--rate    显示速率信息
--sort    对虚拟服务器和真实服务器排序输出
--set tcp tcpfin udp
          设置ipvs连接超时值，三个参数分别代表tcp会话超时时间、收到FIN包后tcp会话超时时间、udp超时时间
--timeout
          显示tcp tcpfin udp的timeout值
--start-daemon
          启动同步守护进程
--stop-daemon
          停止同步守护进程
```

rr（轮循）
wrr（带权轮循）
lc（最小连接）
wlc（权重最小连接）

DR模式环境部署：
三个节点服务器
主服务器Director Server配置：
yum install -y net-tools
yum install -y ipvsadm

vi Dserver.sh
```
#! /bin/bash

echo 1 > /proc/sys/net/ipv4/ip_forward
ipv=/sbin/ipvsadm
vip=10.0.2.90
rs1=10.0.2.62
rs2=10.0.2.63
ifconfig enp0s8:0 down
ifconfig enp0s8:0 $vip broadcast $vip netmask 255.255.255.0 up
route add -host $vip dev enp0s8:0
$ipv -C
$ipv -A -t $vip:80 -s wrr
$ipv -a -t $vip:80 -r $rs1:80 -g -w 3
$ipv -a -t $vip:80 -r $rs2:80 -g -w 1

```
bash Dserver.sh



两台真实服务器Real Server
yum install -y net-tools
yum install -y nginx
配置脚本：
vi Rserver.sh

```
#! /bin/bash
vip=10.0.2.90
ifconfig enp0s8:0 $vip broadcast $vip netmask 255.255.255.0 up
route add -host $vip enp0s8:0
echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
```
bash Rserver.sh

nginx配置略过。改一下index.html就可以测试。



清空策略
/sbin/ipvsadm -C
删除
ifconfig enp0s8:0 del 10.0.2.90

删除先删除主服务器策略，再删除节点服务器策略
添加先添加主服务器策略，再添加节点服务器策略


------------------------


keepalived

下载
wget https://www.keepalived.org/software/keepalived-2.0.17.tar.gz

安装环境依赖包
yum install openssl-devel popt-devel libnl-devel kernel-devel gcc -y

解压编译安装
tar zxvf keepalived-2.0.17.tar.gz
cd keepalived-2.0.17
./configure --prefix=/usr/local/keepalived
make && make install

mkdir -p /etc/keepalived
ln -s /usr/local/keepalived/etc/keepalived/keepalived.conf /etc/keepalived/

修改配置文件
cat keepalived.conf
```
! Configuration File for keepalived

global_defs {
#   notification_email {
#     acassen@firewall.loc
#     failover@firewall.loc
#     sysadmin@firewall.loc
#   }
#   notification_email_from Alexandre.Cassen@firewall.loc
#   smtp_server 192.168.200.1
#   smtp_connect_timeout 30
   router_id LVS_DEVEL
#   vrrp_skip_check_adv_addr
#   vrrp_strict
#   vrrp_garp_interval 0
#   vrrp_gna_interval 0
}

vrrp_instance VI_1 {
    state MASTER   # 角色类型MASTER|BACKUP
    interface enp0s8   # 网卡名称
    virtual_router_id 51   #虚拟路由id（需要与BACKUP一致）
    priority 100   # 优先级
    advert_int 1   # 每1秒检查一次
    #nopreempt     # 非抢占模式
    authentication {
        auth_type PASS   # 认证类型
        auth_pass 1111   # 认证密码
    }
    virtual_ipaddress {
        10.0.2.99
    }
}

virtual_server 10.0.2.99 80 {
    delay_loop 6   # 健康检查时间间隔
    lb_algo wrr    # 负载调度算法
    lb_kind DR     # 负载转发规则
   # persistence_timeout 100   #保持客户端的请求在指定时间内全都发到同一rs上,默认是关闭的，启用会导致轻微的负载不均衡。
   适用于长连接的情况
    protocol TCP

    real_server 10.0.2.62 80 {
        weight 1
        TCP_CHECK {
          connect_port 80
          connect_timeout 3  # 超时时间
          nb_get_retry 3   # 失败重试次数
          delay_before_retry 3   # 失败重试间隔时间
        }
    }
    real_server 10.0.2.63 80 {
        weight 1
        TCP_CHECK {
                connect_port 80
                connect_timeout 3
                nb_get_retry 3
                delay_before_retry 3
        }
    }
}
```
启动服务
systemctl start keepalived

访问VIP进行测试


通常情况，在生产环境下LVS+keepalived的架构，是需要两台主服务器（一主一备,都配置ipvsadm和keepalived），和若干台真实服务器构成。


参考链接
https://www.cnblogs.com/xucoding/p/12160086.html
https://blog.csdn.net/Ki8Qzvka6Gz4n450m/article/details/79119665
