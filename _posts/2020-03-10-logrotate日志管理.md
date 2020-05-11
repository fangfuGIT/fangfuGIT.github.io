---
layout: post
title:  "logrotate日志管理工具"
date:   2020-03-10 17:01:05
categories:  logrotate
tags: logrotate
excerpt: 
mathjax: true
---


参考链接
https://www.cnblogs.com/wushuaishuai/p/9330952.html


我的配置

 cat /etc/logrotate.d/nginxlog 
```
/var/log/nginx/*.log
{
    daily
    rotate 30
    copytruncate
    missingok
    compress
    dateext
    notifempty
    sharedscripts
    postrotate
        sudo nginx -s reload>/dev/null 2>&1
    endscript
}
```
配完之后重新读取配置
/usr/sbin/logrotate /etc/logrotate.conf 

参数说明：   
```
daily 每日切割
rotate 30 转储周期为30天，30天前的日志会被删除
missingok 忽略执行中的错误
copytruncate 用于还在打开中的日志文件，把当前日志备份并截断，是先拷贝再清空的方式，拷贝和清空之间有一个时间差，可能会丢失部分日志数据
compress 通过gzip压缩旧的日志
dateext 指定转储后的日志文件以当前日期为格式结尾
notifempty 如果日志文件是空文件的话，不转储
sharedscripts   所有的日志文件都转储完毕后统一执行一次脚本
```
