---
layout: post
title:  "mysql的笔记"
date:   2018-02-09 14:31:00
categories: mysql
tags: mysql
excerpt: 这是较早前记录在纸本上的一些mysql命令记录,移植到这边来..
mathjax: true
---



```
alter table t2 change b c int(20);
alter table t2 rename t3;
alter table t2 modify a TINYINT;
alter table t2 add(eee int(20) not null comment "eee");
alter table t2 drop colume eee;
```

```
insert into table2(id,title,from_date)values('1001','test','2016-06-21');
update table2 set id = '20001' where title = 'test';
delete from table2 where id = '20001';
```

```
select * from table where id like '20001';
                             not like '%20001%';

select * from tables where id between '10001' and '10010';
select * from tables order by id limit 10,5;
select distinct firstname from table;
select count(*) from table;
       mix(id)
       max(id)
       avg(id)
       sum(id)

```

```
insert into table2 select 字段 from table1;
select 字段1，字段2 from table1 t,table2 g where t.id=g.id and having t.money>=1000;
```

```
create database mydatabase character set utf8; 
mysql -uroot -p -D mydatabase < /root/mydatabse_bak.sql
mysqldump --default-character-set=utf8 -uroot -p --no-autocommit --skip-lock-tables --single-transaction --master-data=2 -q -e -E mydatabase -R  > /root/mydatabse_bak.sql
```

```
show variables like "%bin%";
set global max_binlog_cache_size = 8589934592;
show global variables like 'wait_timeout';
```

查询12.17日-22日单日累积充值1000以上的用户:
```
select concat('d:\\mysql\\bin\\mysql --default-character-set=utf8 -uroot -ppassword -N -h ',f.TelecomIP,' -P 2433 ',s.DbName,' -e "select ',serverid,' serverid,''',servername,''' servername,entryport,t.account,rolename,sum(currencyamt) money,paydate from t_u_pay_order t left join property p on t.account = p.account where t.PayDate >=20171217 and t.PayDate <=20171222 group by t.account,date(t.paydate) having money >= 1000" >> D:\\tongji\\0111.txt') from db_center_game.t_product_server s inner join t_server_fixedassets f on s.masterdbId=f.id where status=1 and s.platid not in(100,999)
```

查找数据库报错的信息
```
find . -name *.err
```

修改字段为自增字段，前提是这个字段必须为主键：
```
alter table table_name modify column id int auto_increment;
```