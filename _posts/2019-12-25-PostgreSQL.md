---
layout: post
title:  "PostgreSQL"
date:   2019-12-24 10:46:02
categories:  
tags: 
excerpt: 
mathjax: true
---





安装postgresql：
```
yum install postgresql-server
postgresql-setup initdb
systemctl enable postgresql.service
systemctl start postgresql.service
systemctl status postgresql.service
```

添加远程访问权限，默认端口5432:
vim /var/lib/pgsql/data/postgresql.conf
```
listen_addresses = '*' 
port = 5432
max_connections = 100 
```
vim /var/lib/pgsql/data/pg_hba.conf
添加两行，第一行是允许本地访问，第二行是允许所有访问
```
host    all             all             127.0.0.1/32            md5		#允许本地访问
host    all             all             all                     md5		#所有IP段访问
host    all             all             192.168.1.0/32          md5		#指定IP段访问
```

修改配置以后重启服务：
systemctl restart postgresql.service


常用命令
```
sudo -i -u postgres
-bash-4.2$ psql
alter user postgres password 'apassword'; # 修改postgres密码
\l   #列出数据库
\l '*post*'   #包含post的数据库
\c dbname # 选择数据库
\d #列出所有表
\q   #退出psql
```

登录命令行：
psql -U postgres -h localhost

创建数据库名为testdb：
createdb -h localhost -p 5432 -U postgres testdb  #直接外部调用createdb建库

日志目录在：/var/lib/pgsql/data/pg_log




