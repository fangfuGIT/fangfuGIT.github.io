---
layout: post
title:  "zabbix没有正常报警的处理"
date:   2018-02-02 16:18:00
categories: zabbix
tags: zabbix,mysql
excerpt: 关于zabbix不报警的检查和处理，并记录了重置zabbix分区表的操作。
mathjax: true
---



这几天公司的DB主从的slave状态，一直莫名中断，查看报错发现是1062，解决这个问题之后，发现另一个问题，zabbix监控没有报警。反复手动关闭mysql的主从状态，就是不报警。

这一套公司的zabbix监控安装运行有两年之久，初始化安装配置也是之前的同事配的，中间没人维护过，也没有留下什么文档。如今出了问题，只能自己着手解决。

先查zabbix_agentd日志，报了一堆：


```
    rm: cannot remove `/root/.bash_history': Permission denied  
    rm: cannot remove `/root/.bash_history': Permission denied  
    rm: cannot remove `/root/.bash_history': Permission denied  
    rm: cannot remove `/root/.bash_history': Permission denied  
    rm: cannot remove `/root/.bash_history': Permission denied  
    rm: cannot remove `/root/.bash_history': Permission denied  
    rm: cannot remove `/root/.bash_history': Permission denied  
```

再查zabbix_server，通过zabbix_get进行测试，测试命令如下：
```
/usr/local/zabbix/bin/zabbix_get -s 192.168.xxx.xxx -p10050 -k "MySQL.running-slave"
```
报下面这个错：
```
ZBX_NOTSUPPORTED: Unsupported item key.
```

初步怀疑是zabbix_agentd的问题，重装zabbix_agentd，重装过程就不再说明了，我是用自己写的脚本装的。

```
    -rw-r--r-- 1 root root 2450 2018/01/31 12:01:47 zabbix_lyb_agentd.sh  
```

重装完成，配置好相关参数，查看zabbix_agentd的日志：

```
tailf /var/log/zabbix/zabbix_agentd.log
```
还是有报错
```
    rm: cannot remove `/root/.bash_history': Permission denied  
```

再测一波mysql主从状态，关闭了slave，然而zabbix监控还是一片平静，什么报警都没有。

再去server端去通过zabbix_get进行测试，发现能够返回值1，说明能获取到监控参数了。

```
    [root@localhost ~]# /usr/local/zabbix/bin/zabbix_get -s 119.29.xxx.xxx -p10050 -k "MySQL.running-slave"    
    1  
```


感觉跟客户端没什么关系了。于是再查看zabbix_server的日志。
```
tailf /tmp/zabbix_server.log
```

报错信息类似如下：
```
Table has no partition for value 1516457600 [insert into history (itemid,clock,num,value,).......
```

很明显，无法写入分区表了，看来需要重新建分区表。

先停zabbix服务
```
    /etc/init.d/zabbix_server stop 
```
查一下数据库的大小，这条SQL语句是在网上找的，果然数据库太大了，

```
    select table_name as "Tables",round(((data_length + index_length) / 1024 / 1024), 2) "Size in MB" from information_schema.TABLES where table_schema = 'zabbix' order by (data_length + index_length) desc;  
```


写个创建分区表的存储过程，借鉴了网上的一些代码，自己稍作修改。编写存储过程代码如下，IP地址我做了处理，请自行修改：


```
    DELIMITER $$  
      
    DROP PROCEDURE IF EXISTS `zabbix`.`partition_create`$$  
      
    CREATE DEFINER=`root`@`192.168.xxx.xxx` PROCEDURE `partition_create`(SCHEMANAME varchar(64), TABLENAME varchar(64), PARTITIONNAME varchar(64), CLOCK int)  
    BEGIN  
       
            DECLARE RETROWS INT;  
            SELECT COUNT(1) INTO RETROWS FROM information_schema.partitions WHERE table_schema = SCHEMANAME AND table_name = TABLENAME AND partition_description >= CLOCK;  
            IF RETROWS = 0 THEN  
            SET @sql = CONCAT( 'ALTER TABLE `', SCHEMANAME, '`.`', TABLENAME, '`',' ADD PARTITION (PARTITION ', PARTITIONNAME, ' VALUES LESS THAN (', CLOCK, '));' );  
            PREPARE STMT FROM @sql;  
            EXECUTE STMT;  
            DEALLOCATE PREPARE STMT;  
            END IF;  
            END$$  
      
    DELIMITER ;  
```


把需要添加分区表的那几张表统计出来，创建和之前表结构一致但带有分区表的数据表。所以记得先把之前的表结构保存出来。然后再执行下面的操作，我这里只以history为例：

```
    use zabbix;  
      
    drop table history;   # 这一条是删表，有重要数据的请慎重执行  
      
    CREATE TABLE `history` (  
      `itemid` bigint(20) unsigned NOT NULL,  
      `clock` int(11) NOT NULL DEFAULT '0',  
      `value` double(16,4) NOT NULL DEFAULT '0.0000',  
      `ns` int(11) NOT NULL DEFAULT '0',  
      KEY `history_1` (`itemid`,`clock`)  
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin  
    partition by range(clock) (  
    PARTITION p20180310 VALUES LESS THAN (UNIX_TIMESTAMP("2018-03-10 00:00:00"))  
    );  
```
然后调用存储过程：

```
    call zabbix.partition_create("zabbix","trends_uint","p20180410",UNIX_TIMESTAMP("2018-04-10 00:00:00"));  
    call zabbix.partition_create("zabbix","trends_uint","p20180510",UNIX_TIMESTAMP("2018-05-10 00:00:00"));  
    call zabbix.partition_create("zabbix","trends_uint","p20180610",UNIX_TIMESTAMP("2018-06-10 00:00:00"));  
    call zabbix.partition_create("zabbix","trends_uint","p20180710",UNIX_TIMESTAMP("2018-07-10 00:00:00"));  
    call zabbix.partition_create("zabbix","trends_uint","p20180810",UNIX_TIMESTAMP("2018-08-10 00:00:00"));  
    call zabbix.partition_create("zabbix","trends_uint","p20180910",UNIX_TIMESTAMP("2018-09-10 00:00:00"));  
    call zabbix.partition_create("zabbix","trends_uint","p20181010",UNIX_TIMESTAMP("2018-10-10 00:00:00"));  
    call zabbix.partition_create("zabbix","trends_uint","p20181110",UNIX_TIMESTAMP("2018-11-10 00:00:00"));  
    call zabbix.partition_create("zabbix","trends_uint","p20181210",UNIX_TIMESTAMP("2018-12-10 00:00:00"));  
    注：UNIX_TIMESTAMP 一般是用于unix的时间戳
```   
  

建议你多添加几条免得以后麻烦，我是添加了一年的，直接在命令里改日期就可以了。

这样重新清理的表包括history、history_uint、history_str、trends、trends_uint...

这些表都是zabbix_server的日志报出来的，

最后，开启zabbix服务

```
    /etc/init.d/zabbix_server start  
```

再查看zabbix_server的日志，没报错了：

```
    [root@localhost ~]# tailf /tmp/zabbix_server.log     
      5209:20180201:153427.380 server #23 started [history syncer #4]  
      5211:20180201:153427.380 server #25 started [proxy poller #1]  
      5190:20180201:153427.396 server #4 started [poller #2]  
      5191:20180201:153427.396 server #5 started [poller #3]  
      5193:20180201:153427.396 server #7 started [poller #5]  
      5189:20180201:153427.399 server #3 started [poller #1]  
      5192:20180201:153427.399 server #6 started [poller #4]  
      5194:20180201:153427.400 server #8 started [unreachable poller #1]  
      5205:20180201:153427.415 server #19 started [discoverer #1]  
      5202:20180201:153427.605 housekeeper [deleted 0 hist/trends, 0 items, 0 events, 0 sessions, 0 alarms, 0 audit items in 0.234616 sec, idle 1 hour(s)]  
```

查看zabbix报警的情况，收到报警信息。成了。

