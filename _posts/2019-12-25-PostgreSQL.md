---
layout: post
title:  "PostgreSQL"
date:   2019-12-24 10:23:12
categories: PostgreSQL 
tags: PostgreSQL
excerpt: PostgreSQL
mathjax: true
---



```
yum install postgresql-server
postgresql-setup initdb
systemctl enable postgresql.service
systemctl start postgresql.service
systemctl status postgresql.service
```


vim /var/lib/pgsql/data/pg_hba.conf

添加两行，第一行是允许本地访问，第二行是允许所有访问
```
host    all             all             127.0.0.1/32            md5
host    all             all             all                     md5
```

systemctl restart postgresql.service



```
sudo -i -u postgres

-bash-4.2$ psql

alter user postgres password 'apassword'; # 修改postgres密码
\l   #列出数据库
\l '*post*'   #包含post的数据库
\c dbname # 切换数据库
\d #列出所有表
\q   #退出psql
```


psql -U postgres -h localhost  # 登录命令行

createdb -h localhost -p 5432 -U postgres testdb  #直接外部调用createdb建库

